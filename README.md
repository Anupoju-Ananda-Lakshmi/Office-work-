traceId: (The Global Journey ID)

What it is: The ID that represents the user's entire click-to-finish journey.

Why we need it: If a user clicks "Generate EOD Report" on the UI, the API Gateway creates a  traceId .
That ID is passed to the Process Service, which passes it to the Communication Service.
If the email fails, DevOps can search Splunk/Kibana for the  traceId  and see the logs for the entire journey across all 3 servers.


How to generate it: Do NOT generate a new one if it already exists! 
Extract it from the incoming HTTP Request Headers (usually  X-B3-TraceId  or  traceparent ) or your Spring Boot Micrometer/Sleuth MDC context.
If (and only if) it is a scheduled background job with no incoming web request, generate a new one.

Java Code (Spring Boot 3 / Micrometer):
String traceId = tracer.currentSpan().context().traceId();
(Fallback: String traceId = "trc-" + UUID.randomUUID().toString();)
