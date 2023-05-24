
yCode, ins.FullName } into gr
             select new MyResultClass
             {
                 Symbol = gr.Key.Symbol,
                 ContractId = gr.Key.ContractId,
                 ExternalId = gr.Key.ExternalId,
                 CurrencyCode = gr.Key.CurrencyCode,
                 FullName = gr.Key.FullName
             };

/////

public (IQueryable<Order> orders, IQueryable<OgbResultClass> oGb) GetIt(long Id, Statement request)
{
    var result = from order in db.Orders
                 join contr in db.Contracts on order.ContractId equals contr.Id
                 join ins in db.Instruments on contr.InstrumentId equals ins.Id
                 join client in db.Counterparties on order.CustomerId equals client.Id
                 where client.Id == Id && (order.EntryDate >= request.DateFrom && order.EntryDate <= request.DateTo)
                       && contr.ExternalId != "KSE:PATT:KMGZ"
                 group new { order, contr, ins } by new { order.ContractId, contr.Symbol, contr.ExternalId, contr.CurrencyCode, ins.FullName } into gr
                 select new
                 {
                     Symbol = gr.Key.Symbol,
                     ContractId = gr.Key.ContractId,
                     ExternalId = gr.Key.ExternalId,
                     CurrencyCode = gr.Key.CurrencyCode,
                     FullName = gr.Key.FullName
                 };

    var orders = result.Select(x => x.order);
    var oGb = result.Select(x => new OgbResultClass
    {
        Symbol = x.Symbol,
        ContractId = x.ContractId,
        ExternalId = x.ExternalId,
        CurrencyCode = x.CurrencyCode,
        FullName = x.FullName
    });

    return (orders, oGb);
}
