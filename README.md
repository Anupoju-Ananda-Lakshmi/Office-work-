spring.application.name=CommunicationService
spring.datasource.driver-class-name=oracle.jdbc.OracleDriver
spring.profiles.active=dev
server.port=8001

# ===================================================================
# DEFAULT ENVIRONMENT NAME FOR FALLBACK
# ===================================================================
app.preIpString=DEV


#====================================================================
#                          RESILIENCE
#====================================================================
resilience4j.retry.instances.bankApiRetry.max-attempts=3
resilience4j.retry.instances.bankApiRetry.wait-duration=2s
resilience4j.retry.instances.bankApiRetry.retry-exceptions[0]=java.io.IOException
resilience4j.retry.instances.bankApiRetry.retry-exceptions[1]=java.net.SocketTimeoutException
resilience4j.retry.instances.bankApiRetry.ignore-exceptions[0]=com.fincore.CommunicationService.exception.CommunicationExceptions.TemplateNotFoundException
resilience4j.retry.instances.bankApiRetry.ignore-exceptions[1]=com.fincore.CommunicationService.exception.CommunicationExceptions.TemplateProcessingException   
