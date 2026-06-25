   
# ===================================================================
#                          CIRCUIT BREAKER
# ===================================================================
resilience4j.circuitbreaker.instances.bankApiCircuit.failure-rate-threshold=50
resilience4j.circuitbreaker.instances.bankApiCircuit.minimum-number-of-calls=5
resilience4j.circuitbreaker.instances.bankApiCircuit.sliding-window-size=10
resilience4j.circuitbreaker.instances.bankApiCircuit.wait-duration-in-open-state=30s
resilience4j.circuitbreaker.instances.bankApiCircuit.permitted-number-of-calls-in-half-open-state=3
