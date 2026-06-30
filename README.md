$F{T66_FLAG}.equalsIgnoreCase("A") ?
new BigDecimal($F{T66_VAL_PY}).divide(new BigDecimal(1000), 0, BigDecimal.ROUND_FLOOR) :
($F{T66_FLAG}.equalsIgnoreCase("P") ?
($F{T66_VAL_PY} == null ? "0%" :
($F{T66_VAL_PY}.contains("%") ?
$F{T66_VAL_PY} :
new BigDecimal($F{T66_VAL_PY}) + "%"))
:
($F{T66_FLAG}.equalsIgnoreCase("T") ?
$F{T66_VAL_PY} : ""))
