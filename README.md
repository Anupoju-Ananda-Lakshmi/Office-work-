package com.fincore.CommunicationService.config;

import com.github.benmanes.caffeine.cache.Caffeine;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.caffeine.CaffeineCacheManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.concurrent.TimeUnit;

/**
 * Caching Configuration for Communication Service.
 * Implements a High-Speed L1 (In-Memory) Cache using Caffeine.
 */
@EnableCaching
@Configuration
public class CacheConfig {

    @Bean
    public CacheManager cacheManager() {
        // Defining the cache names we will use for templates and subjects
        CaffeineCacheManager cacheManager = new CaffeineCacheManager(
                "templates",
                "template_subjects",
                "externalApiConfigs"
        );

        cacheManager.setCaffeine(Caffeine.newBuilder()
                // Evict entries 12 hours after they were written to the cache
                .expireAfterWrite(12, TimeUnit.HOURS)
                // Prevent memory leaks by capping the maximum number of cached templates
                .maximumSize(1000)
                // Record stats for APM (e.g., Datadog/Prometheus) tracking cache hit ratios
                .recordStats());

        return cacheManager;
    }
}
