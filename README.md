package com.fincore.commonutilities.aspect;

import com.fincore.commonutilities.annotation.NoRetry;
import io.github.resilience4j.circuitbreaker.CircuitBreaker;
import io.github.resilience4j.circuitbreaker.CircuitBreakerRegistry;
import io.github.resilience4j.retry.Retry;
import io.github.resilience4j.retry.RetryRegistry;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.core.Ordered;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;
import java.util.function.Supplier;

/**
 * Resilience Aspect.
 * * Responsibilities:
 * 1. Wraps ALL @Service methods with Retry & Circuit Breaker logic.
 * 2. Respects @NoRetry annotation for safety.
 * 3. Runs with HIGHEST_PRECEDENCE to ensure retries happen outside the Transaction boundary.
 *  @author SHUBHANKAR DAS [Emp ID: V1018405]
 *  <a href="mailto:shubhankar.das.cbstcs@sbi.co.in">shubhankar.das.cbstcs@sbi.co.in</a>
 *  @version 1.0.1
 *  @since 2026-05-13
 */
@Aspect
@Component
@Order(Ordered.HIGHEST_PRECEDENCE) // ensures run before @Transactional
@RequiredArgsConstructor
@Slf4j
public class GlobalResilienceAspect {

    private final RetryRegistry retryRegistry;
    private final CircuitBreakerRegistry circuitBreakerRegistry;
    private static final String SERVICE_NAME = "defaultService";

    @Around("within(@org.springframework.stereotype.Service *)")
    public Object applyResilience(ProceedingJoinPoint joinPoint) throws Throwable {

        // 1. Check - Skip if @NoRetry is present
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        if (method.isAnnotationPresent(NoRetry.class)) {
            return joinPoint.proceed();
        }

        // 2. Get Resilience Instances
        Retry retry = retryRegistry.retry(SERVICE_NAME);
        CircuitBreaker circuitBreaker = circuitBreakerRegistry.circuitBreaker(SERVICE_NAME);

        // 3. backend call wrapper
        Supplier<Object> backendCall = () -> {
            try {
                return joinPoint.proceed();
            } catch (Throwable e) {
                throw new RuntimeExceptionWrapper(e);
            }
        };

        // 4. Manual Decoration
        // Logic: CircuitBreaker( Retry( Function ) )
        // If Circuit is Open, we fail FAST. We do NOT want to Retry against an Open circuit.
        // If Circuit is Open -> Fails Fast.
        // If Closed -> Tries Retry Logic.

        // Inner Layer: Retry
        Supplier<Object> retryableSupplier = Retry.decorateSupplier(retry, backendCall);
        // Outer Layer: Circuit Breaker
        Supplier<Object> resilientSupplier = CircuitBreaker.decorateSupplier(circuitBreaker, retryableSupplier);

        // 5. Execute
        try {
            return resilientSupplier.get();
        } catch (RuntimeExceptionWrapper e) {
            // Unwrap original exception for the caller
            throw e.getCause();
        } catch (Exception e) {
            // Throw other exceptions as it is
            throw e;
        }
    }

    /**
     * Internal wrapper to transport checked exceptions through the Supplier lambda.
     */
    private static class RuntimeExceptionWrapper extends RuntimeException {
        public RuntimeExceptionWrapper(Throwable cause) {
            super(cause);
        }
    }
}
