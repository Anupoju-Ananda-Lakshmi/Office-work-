const getPercentage = (npa, advances) => {
  const npaNum = parseFloat(npa) || 0;
  const advancesNum = parseFloat(advances) || 0;

  if (advancesNum <= 0) {
    return "0.0000%";
  }

  const percentage = (npaNum / advancesNum) * 100;

  // Round to 2 decimal places
  const rounded = Math.round(percentage * 100) / 100;

  // Display with 4 decimal places
  return rounded.toFixed(4) + "%";
};
