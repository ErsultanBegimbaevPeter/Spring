        private double SumKzt(int Currency,ExecutedTrades buy, ExecutedTrades sell, BuyInfo buyInfo, SellInfo sellInfo,string type)
        {
            /*
             1 = SELL-if
             2 = Buy-if;
            3 = sell-else;
            4 = buy-else;
             
             */
            if( Currency == 1190)
            {
                if (type.Contains("buy"))
                    return buyInfo.Summ;
                else return sellInfo.Summ;

            }
            else
            {
                if (type.Contains("sell"))
                    return sellInfo.Summ * sell.NbExchangeRate;
                else if(type == "buy-if")
                    return buyInfo.Summ * buy.NbExchangeRate;
                else if (type == "buy-else")
                    return buyInfo.Summ * sell.NbExchangeRate;

            }
            return 0.0;
        }
