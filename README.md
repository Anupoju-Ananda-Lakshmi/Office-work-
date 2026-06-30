   const getPercentage = (npa, advances) => {
      const npaNum = parseFloat(npa) || 0;
      const advancesNum = parseFloat(advances) || 0;
      return advancesNum > 0 ? ((npaNum / advancesNum) * 100).toFixed(4) + '%' : '0.0000%';
    };
