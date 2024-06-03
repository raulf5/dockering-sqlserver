# Install SQL Server for Linux

### Intro

You will use Docker to pull and run the SQL Server 2022 (16.x) Linux container image, [mssql-server-linux](https://mcr.microsoft.com/product/mssql/server/about).

Before starting the following steps, make sure that you select your preferred shell (**bash**, **PowerShell**, or **cmd**)&#x20;

### Tasks

* [ ] Pull the SQL Server Linux container image:

```bash
sudo docker pull mcr.microsoft.com/mssql/server:2022-latest
```

* [ ] Run the container (change the value for `MSSQL_SA_PASSWORD`!!!):

<pre class="language-bash"><code class="lang-bash"><strong>
</strong><strong>sudo docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=&#x3C;YourStrong@Passw0rd>" \
</strong>   -p 1433:1433 --name sql1 --hostname sql1 \
   -d \
   mcr.microsoft.com/mssql/server:2022-latest
</code></pre>

* [ ] Check that the container is up and running from vscode docker extension:

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_More information at_ [_https://learn.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver16\&tabs=cli\&pivots=cs1-bash_](https://learn.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver16\&tabs=cli\&pivots=cs1-bash)
{% endhint %}
