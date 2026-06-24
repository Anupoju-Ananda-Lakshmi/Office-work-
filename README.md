package com.fincore.CommunicationService.service;

import com.fincore.CommunicationService.config.AsyncConfig;
import com.fincore.CommunicationService.dto.Recipients;
import com.fincore.CommunicationService.model.CommAuditLog;
import com.fincore.CommunicationService.repository.CommAuditLogRepository;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.dao.DataAccessException;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;
import org.springframework.transaction.CannotCreateTransactionException;

import java.util.List;

/**
 * <p>A non-blocking auditing service responsible for storing various logging events
 * in the database.</p>
 *
 * <p>This service utilizes a dedicated thread pool defined in the {@link AsyncConfig}
 * class to ensure asynchronous processing without blocking the main application flow.</p>
 *
 * @author SHUBHANKAR (v1018405)
 * @since 2026-06-22
 * @see AsyncConfig
 */
@Slf4j
@Service
@RequiredArgsConstructor
public class CommunicationAuditService {

    private final CommAuditLogRepository auditRepository;

    @Async("auditThreadPool")
    public void logAttempt(String eventId, String txId, String templateId, List<String> channels, Recipients recipients) {
        try {
            String maskedData = maskRecipients(recipients);
            String channelsJoined = channels != null ? String.join(",", channels) : "UNKNOWN";

            CommAuditLog logEntry = CommAuditLog.builder()
                    .eventId(eventId)
                    .txId(txId != null ? txId : "UNKNOWN")
                    .templateId(templateId)
                    .channels(channelsJoined)
                    .maskedRecipients(maskedData)
                    .status("PROCESSING")
                    .build();

            auditRepository.save(logEntry);
            log.debug("Audit: Initial processing logged for eventId={}", eventId);

        } catch (DataAccessException | CannotCreateTransactionException e) {
            log.error("Database connection failed while logging audit attempt for eventId={}. Msg: {}", eventId, e.getMessage());
        }
    }

    @Async("auditThreadPool")
    public void logSuccess(String eventId) {
        try {
            int updatedRows = auditRepository.markAsSuccess(eventId);
            if (updatedRows == 0) {
                log.warn("Audit: Tried to mark eventId={} as SUCCESS, but record was missing.", eventId);
            } else {
                log.debug("Audit: Successfully marked eventId={} as SUCCESS", eventId);
            }
        } catch (DataAccessException e) {
            log.error("Database failure while marking audit success for eventId={}", eventId, e);
        }
    }

    @Async("auditThreadPool")
    public void logFailure(String eventId, String txId, String failureReason) {
        try {
            // 1. Attempt to update the existing row
            int updatedRows = auditRepository.markAsFailed(eventId, truncateReason(failureReason));

            // 2. Race Condition Protection:
            // If the message crashed instantly (e.g., malformed JSON), the DLQ might run BEFORE
            // the initial logAttempt finished. If so, we hard-insert the failure.
            if (updatedRows == 0) {
                CommAuditLog fallbackLog = CommAuditLog.builder()
                        .eventId(eventId)
                        .txId(txId != null ? txId : "UNKNOWN")
                        .templateId("UNKNOWN_OR_CRASHED")
                        .channels("UNKNOWN")
                        .maskedRecipients("UNKNOWN")
                        .status("FAILED")
                        .failureReason(truncateReason(failureReason))
                        .build();
                auditRepository.save(fallbackLog);
                log.warn("Audit: Handled race condition. Hard-inserted FAILED status for eventId={}", eventId);
            } else {
                log.debug("Audit: Marked eventId={} as FAILED in DB", eventId);
            }
        } catch (DataAccessException e) {
            log.error("Database failure while writing DLQ audit for eventId={}", eventId, e);
        }
    }

    private String maskRecipients(Recipients rec) {
        if (rec == null) return "NO_RECIPIENT_DATA";

        StringBuilder masked = new StringBuilder();
        if (rec.getEmail() != null && rec.getEmail().contains("@")) {
            String[] parts = rec.getEmail().split("@");
            String maskedEmail = (parts[0].length() <= 2 ? "**" : parts[0].charAt(0) + "***" + parts[0].charAt(parts[0].length() - 1)) + "@" + parts[1];
            masked.append("E:").append(maskedEmail).append(" ");
        }
        if (rec.getMobile() != null && rec.getMobile().length() >= 4) {
            masked.append("M:******").append(rec.getMobile().substring(rec.getMobile().length() - 4));
        }
        return masked.toString().trim();
    }

    private String truncateReason(String reason) {
        if (reason == null) return "Unknown Failure";
        // Prevent DB clobbering if stack trace is massive
        return reason.length() > 3900 ? reason.substring(0, 3900) + "..." : reason;
    }
}
