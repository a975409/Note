
範例如下：
```C#
public async Task<int> TransferOnSiteImagesToTemporaryAreaAsync(OnSiteImagesTransferToTemporaryAreaDto dto)
{
    foreach (var id in dto.Ids)
    {
	    // ✅ Attach 空殼，設定PK欄位
        var stub = new t_OnSiteVideo { Id = id };
        _context.t_OnSiteVideo.Attach(stub);

        stub.EventId = null;
        stub.AccidentId = null;

		//只標記 EventId / AccidentId 為修改
        _context.Entry(stub).Property(x=>x.EventId).IsModified= true;
        _context.Entry(stub).Property(x => x.AccidentId).IsModified = true;
    }

    return await _context.SaveChangesAsync();
}
```

如果需要預先載入資料的話，則：
```C#
public async Task<int> SetOnSiteImagesAsync(SetOnSiteImagesDto dto)
{
	//查詢條件
    if (DateTime.TryParse(dto.StartTime, out DateTime StartTime) == false)
    {
        StartTime = DateTime.Now;
    }

	//查詢條件
    if (DateTime.TryParse(dto.EndTime, out DateTime EndTime) == false)
    {
        EndTime = DateTime.Now;
    }

	//預先載入資料
    var resultQuery = _context.t_OnSiteVideo.Where(x => x.EventId == null && x.AccidentId == null && x.CreateAt != null && x.CreateAt >= StartTime && x.CreateAt <= EndTime).Select(x => x);

	//查詢條件
    if (string.IsNullOrEmpty(dto.LineGroupId) == false)
    {
        resultQuery = resultQuery.Where(x => x.LineGroupId == dto.LineGroupId);
    }

    // ✅ 只撈 Id，不撈 Video byte[]
    var ids = await resultQuery.Select(x => x.Id).ToListAsync();

    if (!ids.Any()) return 0;

    foreach (var id in ids)
    {
	    // ✅ Attach 空殼，設定PK欄位
        var stub = new t_OnSiteVideo { Id = id };
        _context.t_OnSiteVideo.Attach(stub);

		//只標記 EventId / AccidentId 為修改
        if (dto.Level == 1)//2級事件
        {
            stub.EventId = dto.EventId;
            _context.Entry(stub).Property(x => x.EventId).IsModified = true;
	    }
        else
        {
            stub.AccidentId = dto.EventId;
            _context.Entry(stub).Property(x => x.AccidentId).IsModified = true;
        }
    }

    return await _context.SaveChangesAsync();
}
```