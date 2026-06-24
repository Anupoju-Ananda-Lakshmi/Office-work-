package com.fincore.CommunicationService.service;

import com.fincore.CommunicationService.exception.CommunicationExceptions;
import com.fincore.CommunicationService.model.CommTemplateMaster;
import com.fincore.CommunicationService.model.TemplateId;
import com.fincore.CommunicationService.repository.TemplateRepository;
import freemarker.template.Configuration;
import freemarker.template.Template;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.cache.annotation.CacheEvict;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

import java.io.IOException;
import java.io.StringReader;

/**
 * <p>Handles retrieval and caching of compiled FreeMarker templates.</p>
 *
 * <p>This service isolates caching logic from business logic, ensuring clean separation
 * of concerns and proper Spring AOP proxy interception without self-invocation hacks.</p>
 *
 * @author SHUBHANKAR (v1018405)
 * @since 2026-06-22
 */
@Slf4j
@Service
@RequiredArgsConstructor
public class TemplateCacheService {

    private final TemplateRepository templateRepository;
    private final Configuration freeMarkerConfig;

    /**
     * <p>Fetches the template CLOB from Oracle and compiles it into a FreeMarker {@link Template} object.</p>
     *
     * <p><strong>Caching Strategy:</strong></p>
     * <ul>
     *   <li><strong>Key:</strong> Composite ({@code code:channel:language}).</li>
     *   <li><strong>TTL:</strong> 12 hours (configured externally).</li>
     *   <li><strong>Condition:</strong> Caches only if result is not {@code null}.</li>
     * </ul>
     *
     * @param templateCode The unique template identifier.
     * @param channel      The communication channel.
     * @param language     The locale language code.
     * @return The compiled FreeMarker {@link Template} object.
     * @throws CommunicationExceptions.TemplateNotFoundException   if the template is not found or inactive.
     * @throws CommunicationExceptions.TemplateProcessingException if compilation fails.
     */
    @Cacheable(value = "templates", key = "#templateCode + ':' + #channel + ':' + #language", unless = "#result == null")
    public Template getCompiledTemplate(String templateCode, String channel, String language) {
        log.info("L1 CACHE MISS: Fetching and Compiling Template {} for {} from Oracle DB.", templateCode, channel);

        // 1. Database Lookup
        CommTemplateMaster tmpl = templateRepository.findById(new TemplateId(templateCode, channel, language))
                .orElseThrow(() -> new CommunicationExceptions.TemplateNotFoundException("Template not found in Oracle: " + templateCode));

        if (!"Y".equalsIgnoreCase(tmpl.getIsActive().trim())) {
            throw new CommunicationExceptions.TemplateNotFoundException("Template is disabled: " + templateCode);
        }

        String rawClobContent = tmpl.getBodyContent();

        // 2. Compile into Abstract Syntax Tree (AST)
        try {
            return new Template(templateCode, new StringReader(rawClobContent), freeMarkerConfig);
        } catch (IOException e) {
            log.error("Syntax error in FreeMarker template {}.", templateCode, e);
            throw new CommunicationExceptions.TemplateProcessingException("Syntax error compiling template: " + templateCode, e);
        }
    }

    /**
     * <p>Retrieves the cached subject line for a specific template.</p>
     *
     * @param templateCode The unique template identifier.
     * @param channel      The communication channel.
     * @param language     The locale language code.
     * @return The subject line string.
     * @throws CommunicationExceptions.TemplateNotFoundException if the template does not exist.
     */
    @Cacheable(value = "template_subjects", key = "#templateCode + ':' + #channel + ':' + #language")
    public String getTemplateSubject(String templateCode, String channel, String language) {
        CommTemplateMaster tmpl = templateRepository.findById(new TemplateId(templateCode, channel, language))
                .orElseThrow(() -> new CommunicationExceptions.TemplateNotFoundException("Template not found in Oracle: " + templateCode));

        if (!"Y".equalsIgnoreCase(tmpl.getIsActive().trim())) {
            throw new CommunicationExceptions.TemplateNotFoundException("Template is disabled: " + templateCode);
        }

        return tmpl.getSubject();
    }

    /**
     * <p>Evicts all entries from the template caches.</p>
     *
     * @see TemplateEngineService#flushTemplateCache()
     */
    @CacheEvict(value = {"templates", "template_subjects"}, allEntries = true)
    public void flushCache() {
        log.warn("ADMIN ACTION: All communication templates evicted from L1/L2 Cache.");
    }
}
