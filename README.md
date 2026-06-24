package com.fincore.CommunicationService.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Executor;

/**
 * Asynchronous Configuration.
 * Creates dedicated threads for non-blocking I/O operations (like Database Auditing).
 */
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean(name = "auditThreadPool")
    public Executor auditThreadPool() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(20);
        // Allows up to 1000 audit events to queue up during extreme traffic bursts
        executor.setQueueCapacity(1000);
        executor.setThreadNamePrefix("Audit-Async-");
        // Rejects tasks if queue is full instead of crashing JVM memory
        executor.setRejectedExecutionHandler(new java.util.concurrent.ThreadPoolExecutor.CallerRunsPolicy());
        executor.initialize();
        return executor;
    }
}
