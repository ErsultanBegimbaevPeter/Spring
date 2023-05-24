
var result = from order in db.Orders
             join contr in db.Contracts on order.ContractId equals contr.Id
             join ins in db.Instruments on contr.InstrumentId equals ins.Id
             join client in db.Counterparties on order.CustomerId equals client.Id
             where client.OcrmId == request.OcrmId && (order.EntryDate >= request.DateFrom && order.EntryDate <= request.DateTo)
                   && contr.ExternalId != "KSE:PATT:KMGZ"
             group new { order, contr, ins } by new { order.ContractId, contr.Symbol, contr.ExternalId, contr.CurrencyCode, ins.FullName } into gr
             select new MyResultClass
             {
                 Symbol = gr.Key.Symbol,
                 ContractId = gr.Key.ContractId,
                 ExternalId = gr.Key.ExternalId,
                 CurrencyCode = gr.Key.CurrencyCode,
                 FullName = gr.Key.FullName
             };

IQueryable<MyResultClass> orders = result.SelectMany(x => x.Orders).AsQueryable();
IQueryable<MyResultClass> oGb = result.Select(x => new MyResultClass
                              {
                                  Symbol = x.Symbol,
                                  ContractId = x.ContractId,
                                  ExternalId = x.ExternalId,
                                  CurrencyCode = x.CurrencyCode,
                                  FullName = x.FullName
                              }).AsQueryable();
