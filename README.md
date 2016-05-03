## ทดสอบ Entity Framework บน Mac

- Repository นี้สามารถ Connect MSSQL ได้ปกติ
- โปรแกรมสามารถอ่าน Config `SimpleContext` ได้ถูกต้อง

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections> </configSections>
  <connectionStrings>
    <add name="SimpleContext" providerName="System.Data.SqlClient" connectionString="Data Source=192.168.0.105\SQLEXPRESS;Database=EFMigration;User Id=sa;Password=abcABC123;" />
  </connectionStrings>
</configuration>
```

## ปัญหาที่เจอกับโปรเจคอื่นที่ใช้ `MySQL`

- Entity Framework อ่าน Connection name จากไฟล์ Config ไม่ถูกต้อง
- Error `System.InvalidOperationException : No connection string named 'test' could be found in the application config file. `
- โดย Error นี้จะเกิดเฉพาะบน Mac เท่านั้น เนื่องจากนำโค้ดเดียวกันไปรันบน Windows จะทำงานปกติ

```csharp
[Test]
public void ShouldCreateSchema ()
{
    using (var context = new EFContext ("name=test")) {
        if (!context.Database.Exists ()) {
            context.Customers.Add (new Customer { });
            context.SaveChanges ();
        }
    }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <system.data>
        <DbProviderFactories>
            <remove invariant="MySql.Data.MySqlClient" />
            <add name="MySQL Data Provider" invariant="MySql.Data.MySqlClient" description=".Net Framework Data Provider for MySQL" type="MySql.Data.MySqlClient.MySqlClientFactory, MySql.Data, Version=6.9.8.0, Culture=neutral, PublicKeyToken=c5687fc88969c44d" />
        </DbProviderFactories>
    </system.data>
    <entityFramework>
        <providers>
            <provider invariantName="MySql.Data.MySqlClient" type="MySql.Data.MySqlClient.MySqlProviderServices, MySql.Data.Entity.EF6, Version=6.9.8.0, Culture=neutral, PublicKeyToken=c5687fc88969c44d">
            </provider>
        </providers>
    </entityFramework>
    <connectionStrings>
        <add name="test" providerName="MySql.Data.MySqlClient" connectionString="Server=localhost; User Id=root;Password=1234;Database=EFMigration" />
    </connectionStrings>
</configuration>
```