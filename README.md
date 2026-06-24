package com.fincore.CommunicationService.model;

import jakarta.persistence.Column;
import jakarta.persistence.Embeddable;
import lombok.Getter;
import lombok.Setter;

import java.io.Serializable;
import java.util.Objects;

@Setter
@Getter
@Embeddable
public class TemplateId implements Serializable {

    @Column(name = "TEMPLATE_CODE", length = 100, nullable = false)
    private String templateCode;

    @Column(name = "CHANNEL", length = 20, nullable = false)
    private String channel;

    @Column(name = "LANGUAGE_CODE", length = 5, nullable = false)
    private String languageCode;

    public TemplateId() {
    }

    public TemplateId(String templateCode, String channel, String languageCode) {
        this.templateCode = templateCode;
        this.channel = channel;
        this.languageCode = languageCode;
    }

    // Equals and HashCode are MANDATORY for composite keys
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        TemplateId that = (TemplateId) o;
        return Objects.equals(templateCode, that.templateCode) &&
                Objects.equals(channel, that.channel) &&
                Objects.equals(languageCode, that.languageCode);
    }

    @Override
    public int hashCode() {
        return Objects.hash(templateCode, channel, languageCode);
    }
}



package com.fincore.CommunicationService.model;

import jakarta.persistence.*;
import lombok.Data;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;
import java.time.LocalDateTime;

@Data
@Entity
@Table(name = "COMM_TEMPLATE_MASTER")
public class CommTemplateMaster {

    @EmbeddedId
    private TemplateId id;

    @Column(name = "SUBJECT", length = 255)
    private String subject;

    @Lob
    @Column(name = "BODY_CONTENT", nullable = false, columnDefinition = "CLOB")
    private String bodyContent;

    @Column(name = "IS_ACTIVE", length = 1, nullable = false)
    private String isActive;

    @Column(name = "CREATED_BY", length = 50, nullable = false)
    private String createdBy;

    @CreationTimestamp
    @Column(name = "CREATED_AT", nullable = false, updatable = false)
    private LocalDateTime createdAt;

    @Column(name = "UPDATED_BY", length = 50)
    private String updatedBy;

    @UpdateTimestamp
    @Column(name = "UPDATED_AT")
    private LocalDateTime updatedAt;

}



