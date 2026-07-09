LocalDate.parse(etlDate, DateTimeFormatter.ofPattern("dd-MM-yy"))
         .format(DateTimeFormatter.ofPattern("ddMMyyyy"));
