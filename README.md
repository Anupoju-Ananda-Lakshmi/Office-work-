net.sf.jasperreports.engine.JRException: net.sf.jasperreports.engine.fill.JRExpressionEvalException: Error evaluating expression for source text: $F{T66_FLAG}.equalsIgnoreCase( "A") ? 
new BigDecimal($F{T66_VAL_PY}).divide(new BigDecimal(1000),0,BigDecimal.ROUND_FLOOR) :
($F{T66_FLAG}.equalsIgnoreCase( "P") ? 
(new BigDecimal($F{T66_VAL_PY}))+"%": 
	($F{T66_FLAG}.equalsIgnoreCase( "T") ? $F{T66_VAL_PY} : ""))

	at com.jaspersoft.studio.editor.preview.view.control.ReportController.fillReport(ReportController.java:553)

	at com.jaspersoft.studio.editor.preview.view.control.ReportController.access$18(ReportController.java:528)

	at com.jaspersoft.studio.editor.preview.view.control.ReportController$1.run(ReportController.java:446)

	at org.eclipse.core.internal.jobs.Worker.run(Worker.java:63)

Caused by: net.sf.jasperreports.engine.fill.JRExpressionEvalException: Error evaluating expression for source text: $F{T66_FLAG}.equalsIgnoreCase( "A") ? 
new BigDecimal($F{T66_VAL_PY}).divide(new BigDecimal(1000),0,BigDecimal.ROUND_FLOOR) :
($F{T66_FLAG}.equalsIgnoreCase( "P") ? 
(new BigDecimal($F{T66_VAL_PY}))+"%": 
	($F{T66_FLAG}.equalsIgnoreCase( "T") ? $F{T66_VAL_PY} : ""))

	at net.sf.jasperreports.engine.fill.JREvaluator.handleEvaluationException(JREvaluator.java:294)

	at net.sf.jasperreports.engine.fill.JREvaluator.evaluate(JREvaluator.java:328)

	at net.sf.jasperreports.engine.fill.JRCalculator.evaluate(JRCalculator.java:673)

	at net.sf.jasperreports.engine.fill.JRCalculator.evaluate(JRCalculator.java:641)

	at net.sf.jasperreports.engine.fill.JRFillElement.evaluateExpression(JRFillElement.java:1202)

	at net.sf.jasperreports.engine.fill.JRFillTextField.evaluateText(JRFillTextField.java:555)

	at net.sf.jasperreports.engine.fill.JRFillTextField.evaluate(JRFillTextField.java:540)

	at net.sf.jasperreports.engine.fill.JRFillElementContainer.evaluate(JRFillElementContainer.java:383)

	at net.sf.jasperreports.engine.fill.JRFillBand.evaluate(JRFillBand.java:548)

	at net.sf.jasperreports.engine.fill.JRVerticalFiller.fillColumnBand(JRVerticalFiller.java:2613)

	at net.sf.jasperreports.engine.fill.JRVerticalFiller.fillDetail(JRVerticalFiller.java:836)

	at net.sf.jasperreports.engine.fill.JRVerticalFiller.fillReportContent(JRVerticalFiller.java:296)

	at net.sf.jasperreports.engine.fill.JRVerticalFiller.fillReport(JRVerticalFiller.java:123)

	at net.sf.jasperreports.engine.fill.JRBaseFiller.fill(JRBaseFiller.java:622)

	at net.sf.jasperreports.engine.fill.BaseFillHandle$ReportFill.run(BaseFillHandle.java:135)

	at java.base/java.lang.Thread.run(Unknown Source)

Caused by: java.lang.NumberFormatException: Character % is neither a decimal digit number, decimal point, nor "e" notation exponential mark.

	at java.base/java.math.BigDecimal.<init>(Unknown Source)

	at java.base/java.math.BigDecimal.<init>(Unknown Source)

	at java.base/java.math.BigDecimal.<init>(Unknown Source)

	at FR_TBL_66_1782822640849_269570.evaluate(FR_TBL_66_1782822640849_269570:157)

	at net.sf.jasperreports.engine.fill.JREvaluator.evaluate(JREvaluator.java:313)

	... 14 more


i have given the below expression for text field in java and i got the error above
$F{T66_FLAG}.equalsIgnoreCase( "A") ? 
new BigDecimal($F{T66_VAL_PY}).divide(new BigDecimal(1000),0,BigDecimal.ROUND_FLOOR) :
($F{T66_FLAG}.equalsIgnoreCase( "P") ? 
(new BigDecimal($F{T66_VAL_PY}))+"%": 
	($F{T66_FLAG}.equalsIgnoreCase( "T") ? $F{T66_VAL_PY} : ""))
