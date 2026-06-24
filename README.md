package com.fincore.CommunicationService.service;

import com.fincore.CommunicationService.exception.CommunicationExceptions;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import freemarker.template.Template;
import freemarker.template.TemplateException;

import java.io.IOException;
import java.io.StringWriter;
import java.util.Map;

/**
 * <p>A high-speed template rendering engine optimized for low-latency OTP delivery.</p>
 *
 * <p>This service delegates caching and compilation to {@link TemplateCacheService},
 * ensuring strict separation of concerns and thread-safe proxy interception.</p>
 *
 * @author SHUBHANKAR (v1018405)
 * @since 2026-06-22
 * @see TemplateCacheService
 */
@Slf4j
@Service
@RequiredArgsConstructor
public class TemplateEngineService {

    private final TemplateCacheService templateCacheService;

    /**
     * <p>Renders the final output string by merging dynamic data into a pre-compiled template.</p>
     *
     * <p><strong>Security Note:</strong> This method is <em>not</em> cached. It processes dynamic
     * variables on every request to prevent cross-pollination data leaks.</p>
     *
     * @param templateCode The unique identifier for the template.
     * @param channel The communication channel.
     * @param language The locale language code.
     * @param templateData A map of dynamic key-value pairs to bind to the template.
     * @return The fully rendered string ready for delivery.
     * @throws CommunicationExceptions.TemplateProcessingException if variable binding fails.
     */
    public String renderTemplate(String templateCode, String channel, String language, Map<String, Object> templateData) {
        // 1. Fetch the cached, pre-compiled Template AST Object from the dedicated cache service
        Template compiledTemplate = templateCacheService.getCompiledTemplate(templateCode, channel, language);

        // 2. Merge dynamic data (OTP, Names) into the template
        try (StringWriter writer = new StringWriter()) {
            compiledTemplate.process(templateData, writer);
            return writer.toString();
        } catch (IOException | TemplateException e) {
            log.error("Fatal error binding dynamic variables to template {}.", templateCode, e);
            throw new CommunicationExceptions.TemplateProcessingException("Failed to bind dynamic variables to template: " + templateCode, e);
        }
    }

    /**
     * <p>Public API to retrieve a template subject.</p>
     *
     * @param templateCode The unique identifier for the template.
     * @param channel The communication channel.
     * @param language The locale language code.
     * @return The subject line string.
     */
    public String getTemplateSubject(String templateCode, String channel, String language) {
        return templateCacheService.getTemplateSubject(templateCode, channel, language);
    }

    /**
     * <p>Emergency cache flush mechanism.</p>
     *
     * <p>Delegates to {@link TemplateCacheService} to evict all cached templates.</p>
     */
    public void flushTemplateCache() {
        templateCacheService.flushCache();
    }
}
