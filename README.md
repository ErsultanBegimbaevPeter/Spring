if
sell :   sellInfo.SummKzt = sell.CurrencyCode == 1190 ? sellInfo.Summ : sellInfo.Summ * sell.NbExchangeRate;
buy  :   buyInfo.SummKzt = sell.CurrencyCode == 1190 ? buyInfo.Summ : buyInfo.Summ * buy.NbExchangeRate;

else
sell :   sellInfo.SummKzt = sell.CurrencyCode == 1190 ? sellInfo.Summ : sellInfo.Summ * sell.NbExchangeRate;
buy  :   buyInfo.SummKzt = sell.CurrencyCode == 1190 ? buyInfo.Summ : buyInfo.Summ * sell.NbExchangeRate;
