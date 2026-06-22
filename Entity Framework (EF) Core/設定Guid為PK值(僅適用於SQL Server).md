> 自動產生的Guid值，需設定由SQL端產生(呼叫NEWSEQUENTIALID())，SQL產生的Guid值會有連續性，比較不會有索引破碎的問題

在`DBContext`內的 `OnModelCreating` 設定如下：
```C#
public partial class EVTContext : DbContext
{
    public EVTContext(DbContextOptions<EVTContext> options)
        : base(options)
    {
    }
	
	public virtual DbSet<TRefreshToken> TRefreshTokens { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<TRefreshToken>(entity =>
        {
            entity.HasKey(e => e.FId);

            entity.ToTable("t_refreshToken");

            // ── PK：GUID，由 DB 端 NEWSEQUENTIALID() 產生 ──
            entity.Property(e => e.FId)
                .HasColumnName("f_id")
                .HasColumnType("uniqueidentifier")
                .ValueGeneratedOnAdd()　// EF Core 標記為 DB 產生
                .HasDefaultValueSql("NEWSEQUENTIALID()"); // 對應 DB Default
        });
    }
}
```

.NET 9 以上版本支援產生循序性的Guid值：
```C#
Guid.CreateVersion7()
```