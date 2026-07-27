package com.fincore.TemplateConfigurationService.validator;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.node.ArrayNode;
import com.fincore.TemplateConfigurationService.dto.TemplatePayload;
import com.fincore.TemplateConfigurationService.dto.VariantDto;
import com.fincore.TemplateConfigurationService.dto.VariantParamDto;
import com.fincore.TemplateConfigurationService.exception.BadRequestException;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;

import java.util.HashSet;
import java.util.List;
import java.util.Set;

@Slf4j
@Component
@RequiredArgsConstructor
public class TemplatePayloadValidator {

    private final ObjectMapper objectMapper;

    public void validate(TemplatePayload payload) {
        if (payload == null) {
            throw new BadRequestException("Payload must not be null");
        }

        TemplatePayload.Template meta = payload.getTemplate();
        if (meta == null || meta.getTemplateMeta() == null) {
            throw new BadRequestException("template.templateMeta section is mandatory");
        }

        String templateId = meta.getTemplateMeta().getTemplateId();
        if (templateId == null || templateId.isBlank()) {
            throw new BadRequestException("templateId is mandatory");
        }

        // Variant-level validations
        List<VariantDto> variants = payload.getVariants();

        Set<String> variantCodes = new HashSet<>();
        for (VariantDto v : variants) {
            if (!variantCodes.add(v.getVariantCode())) {
                throw new BadRequestException("Duplicate variantCode: " + v.getVariantCode());
            }
            validateParams(v);
        }

        // Lightweight JSON check for template meta+data
        try {
            objectMapper.writeValueAsString(meta.getReportData());
        } catch (JsonProcessingException e) {
            throw new BadRequestException("reportData is not serializable JSON");
        }
    }

    private void validateParams(VariantDto variant) {
        List<VariantParamDto> params = variant.getParams();
        if (params == null) {
            return;
        }

        Set<String> paramNames = new HashSet<>();
        for (VariantParamDto p : params) {
            if (!paramNames.add(p.getParamName())) {
                throw new BadRequestException(
                        "Duplicate paramName '" + p.getParamName() + "' in variant " + variant.getVariantCode());
            }
        }
    }

    //Validate payload before save

    public void validatePayloadBeforeSave(TemplatePayload payload) {

        if (payload == null) {
            throw new BadRequestException("Payload cannot be null");
        }

        if (payload.getTemplate() == null ||
                payload.getTemplate().getTemplateMeta() == null) {
            throw new BadRequestException("Template metadata is mandatory");
        }

        String templateId = payload.getTemplate()
                .getTemplateMeta()
                .getTemplateId();

        if (templateId == null || templateId.isBlank()) {
            throw new BadRequestException("templateId is mandatory");
        }

        List<VariantDto> variants = payload.getVariants();
        if (variants == null || variants.isEmpty()) {
            throw new BadRequestException("At least one variant is required");
        }

        // 🔴 Validate duplicate variant codes in request
        Set<String> variantCodes = new HashSet<>();
        for (VariantDto v : variants) {

            if (v.getVariantCode() == null || v.getVariantCode().isBlank()) {
                throw new BadRequestException("variantCode is mandatory");
            }

            if (!variantCodes.add(v.getVariantCode())) {
                throw new BadRequestException(
                        "Duplicate variantCode found: " + v.getVariantCode()
                );
            }

            // Validate params
            if (v.getParams() != null) {
                Set<String> paramNames = new HashSet<>();
                for (VariantParamDto p : v.getParams()) {

                    if (p.getParamName() == null || p.getParamName().isBlank()) {
                        throw new BadRequestException(
                                "paramName is mandatory for variant " + v.getVariantCode()
                        );
                    }

                    if (!paramNames.add(p.getParamName())) {
                        throw new BadRequestException(
                                "Duplicate paramName '" + p.getParamName() +
                                        "' in variant " + v.getVariantCode()
                        );
                    }
                }
            }
        }
    }




    // Validate the JSON only

    public void validateTemplateJson(String templateJson) {

        if (templateJson == null || templateJson.isBlank()) {
            throw new BadRequestException("templateJson cannot be empty");
        }

        JsonNode root;
        try {
            root = objectMapper.readTree(templateJson);
        } catch (Exception e) {
            throw new BadRequestException("Invalid JSON format in templateJson");
        }

        // -------- REQUIRED FIELDS CHECK ----------
        validateRequiredField(root, "rows");      // must exist
        validateRequiredField(root, "columns");   // must exist
        validateRequiredField(root, "cells");     // must exist

        // -------- TYPE CHECKS ----------
        if (!root.get("rows").isInt() || root.get("rows").asInt() <= 0) {
            throw new BadRequestException("rows must be a positive integer");
        }

        if (!root.get("columns").isInt() || root.get("columns").asInt() <= 0) {
            throw new BadRequestException("columns must be a positive integer");
        }

        if (!root.get("cells").isArray()) {
            throw new BadRequestException("cells must be an array");
        }

        // -------- VALIDATE CELL STRUCTURE ----------
        int rows = root.get("rows").asInt();
        int cols = root.get("columns").asInt();
        ArrayNode cells = (ArrayNode) root.get("cells");

        for (JsonNode rowNode : cells) {

            if (!rowNode.isArray()) {
                throw new BadRequestException("Each row inside cells must be an array");
            }

            if (rowNode.size() != cols) {
                throw new BadRequestException("Each row must contain exactly " + cols + " cells");
            }

            for (JsonNode cell : rowNode) {
                validateCell(cell);
            }
        }
    }

    // ---------------- Helper Methods ----------------

    private void validateRequiredField(JsonNode root, String fieldName) {
        if (!root.has(fieldName)) {
            throw new BadRequestException("Missing required field: " + fieldName);
        }
    }

    private void validateCell(JsonNode cell) {
        // Example checks — adjust per your AllowedColumns metadata rules

        if (!cell.has("type")) {
            throw new BadRequestException("Each cell must contain 'type'");
        }

        if (!cell.has("value")) {
            throw new BadRequestException("Each cell must contain 'value'");
        }

        // Type must be string
        if (!cell.get("type").isTextual()) {
            throw new BadRequestException("Cell 'type' must be a string");
        }
    }
}
