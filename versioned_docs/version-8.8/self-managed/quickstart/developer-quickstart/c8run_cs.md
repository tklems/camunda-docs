# Rychlý start pro vývojáře – Camunda 8 Run
---
Rychlý start pro vývojáře aplikací, nasazení Camunda 8 Self-Managed do lokálního orchestration clusteru pomocí Camunda 8 Run.
---

:::note
Camunda 8 Run není podporována pro produkční prostředí.
:::

Camunda 8 Run umožňuje spustit orchestration cluster, který zahrnuje Zeebe, Operate, Tasklist, Identity a Elasticsearch s minimální konfigurací. Je určena pro vývojáře, kteří chtějí modelovat BPMN diagramy, nasazovat je a pracovat s běžícími instancemi procesů v jednoduchém lok8ln9m prostředí. Tento návod vysvětluje, jak začít na lokálním nebo virtuálním stroji.

Camunda 8 Run obsahuje:

- Orchestration Cluster
- Connectors
- Elasticsearch

Camunda 8 Run také podporuje ukládání a správu dokumentů pomocí document handlingu.

## Požadavky

- **OpenJDK 21–23**: Vyžadováno pro běh Camunda 8 jako Java aplikace.
- **Docker 20.10.21+**: Vyžadováno pro běh Camunda 8 přes Docker Compose.
- **Desktop Modeler**
- **Pokud používáte Ubuntu**: Ubuntu 22.04 nebo novější

:::note
Po instalaci OpenJDK ověřte nastavení `JAVA_HOME` spuštěním `java -version` v terminálu.

Pokud se žádná verze Javy nenajde, před pokračováním postupujte podle návodu k instalaci OpenJDK a nastavte `JAVA_HOME`.
:::

## Instalace a spuštění Camunda 8 Run

1. Stáhněte nejnovější vydání <C8Run/> pro svůj operační systém a architekturu. Otevřením souboru `.tgz` se skript Camunda 8 Run rozbalí do nového adresáře.
2. Přejděte do nového adresáře `c8run`.
3. Spusťte Camunda 8 Run jedním z následujících příkazů v terminálu:

- Na Mac a Linuxu:
  - Spusťte pomocný skript: `./start.sh`
  - Nebo použijte CLI příkaz: `./c8run start`
- Na Windows:
  - Použijte CLI příkaz: `.\c8run.exe start`

Pokud se aplikace úspěšně spustí, otevře se automaticky okno Operate v prohlížeči. Alternativně můžete Operate otevřít na adrese [http://localhost:8080/operate](http://localhost:8080/operate).

Pro spuštění Camunda 8 v Docker Compose pomocí C8Run můžete použít následující volbu. Ekvivalentní spuštění je `docker compose up -d`:

- Na Mac a Linuxu: `./start.sh --docker`
- Na Windows: `.\c8run.exe start --docker`

Při spuštění v Dockeru bude Operate dostupný na [http://localhost:8088/operate](http://localhost:8088/operate).

:::note
Pokud se Camunda 8 Run nespustí, spusťte [shutdown script](#shut-down-camunda-8-run) pro ukončení aktuálních procesů a poté znovu spusťte startovací skript.
:::

### Konfigurační volby

Následující volby umožňují pohodlně přepsat nastavení pro rychlé testy a práci v Camunda 8 Run.  
Pro pokročilejší nebo trvalou konfiguraci upravte výchozí `configuration/application.yaml` nebo dodejte vlastní soubor pomocí příznaku `--config` (např. [pro zapnutí ověřování a autorizace](#enable-authentication-and-authorization)).

| Argument                   | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--config <path>`          | Použije zadaný Zeebe [`application.yaml`](/self-managed/components/orchestration-cluster/zeebe/configuration/configuration.md).                                                                                                                                                                                                                                                                                                        |
| `--username <arg>`         | Nastaví uživatelské jméno prvního uživatele na `<arg>`.                                                                                                                                                                                                                                                                                                                                                                                |
| `--password <arg>`         | Nastaví heslo prvního uživatele na `<arg>`.                                                                                                                                                                                                                                                                                                                                                                                            |
| `--keystore <arg>`         | Nakonfiguruje TLS certifikát pro HTTPS. Pokud není zadán, použije se HTTP. Další informace viz [enabling TLS](#enable-tls).                                                                                                                                                                                                                                                                                                            |
| `--keystorePassword <arg>` | Zadá heslo pro JKS keystore soubor.                                                                                                                                                                                                                                                                                                                                                                                                    |
| `--port <arg>`             | Nastaví port Camunda core (výchozí: `8080`).                                                                                                                                                                                                                                                                                                                                                                                          |
| `--log-level <arg>`        | Nastaví úroveň logování pro Camunda core.                                                                                                                                                                                                                                                                                                                                                                                              |
| `--docker`                 | Stáhne a spustí Camunda Docker Compose distribuci. Tato volba nabízí snadnou zkratku pro běh Camunda v Docker Compose. Další volby C8Run však nejsou podporovány a budou ignorovány. Další informace o běhu Camunda s Docker Compose najdete v dokumentaci. Informace o zastavení Docker aplikace viz [shutdown script](#shut-down-camunda-8-run).                                     |
| `--disable-elasticsearch`  | Zabrání spuštění vestavěného Elasticsearch. Zajistěte jinou instanci Elasticsearch pomocí `--config`. Podrobnosti najdete v části [external Elasticsearch](#start-external-elasticsearch).                                                                                                                                                                                                                                             |
| `--startup-url`            | URL, která se otevře po startu (např. `'http://localhost:8080/operate'`). Ve výchozím nastavení se otevře Operate.                                                                                                                                                                                                                                                                                                                    |

## Práce s Camunda 8 Run

### Přístup ke komponentám Camunda

Camunda 8 Run používá jednoduché ověřování s demo/demo pro všechna webová rozhraní. OIDC/Keycloak není součástí této distribuce.
Do všech webových rozhraní se přihlásíte pomocí výchozích údajů:

- **Username:** `demo`
- **Password:** `demo`

Tato webová rozhraní jsou dostupná na adresách:

- **Operate:** http://localhost:8080/operate
- **Tasklist:** http://localhost:8080/tasklist
- **Identity:** http://localhost:8080/identity

Následující komponenty nemají webové rozhraní, ale jejich endpointy se hodí pro další konfiguraci:

- **Orchestration Cluster REST API:** http://localhost:8080/v2/
- **Inbound Connectors API:** http://localhost:8086/
- **Zeebe API (gRPC):** http://localhost:26500/
- **Metrics (Prometheus):** http://localhost:9600/actuator/prometheus
- **Swagger UI (API Explorer):** http://localhost:8080/swagger-ui/index.html

:::note

- URL pro aplikaci Docker Compose najdete v dokumentaci [Docker Compose](#docker-compose).
- Connectors API neposkytuje webové rozhraní. Při otevření URL v prohlížeči se může zobrazit přihlašovací stránka, ale nelze ji použít pro přihlášení. Použijte přímo API endpointy.
  :::

### Nasazení diagramů z Desktop Modeler

Než budete pokračovat, ujistěte se, že máte nainstalovaný Desktop Modeler.

Pro nasazení diagramů z Desktop Modeleru použijte následující konfiguraci:

- **Target:** Self-Managed
- **Cluster endpoint:** `http://localhost:26500` (Zeebe Gateway)
- **Authentication:** None

Po dokončení se zobrazí notifikace o úspěchu. Spusťte novou instanci procesu, abyste viděli běžící proces v Operate.

### Použití vestavěných a vlastních konektorů

Desktop Modeler automaticky stahuje šablony pro předpřipravené konektory. Vlastní konektory lze také přidat do distribuce Camunda 8 Run.

Chcete-li přidat vlastní konektor:

1. Umístěte `.jar` soubor konektoru do složky `/custom_connectors` v adresáři `/c8run`.
2. Umístěte element template do příslušné složky pro vaši instalaci.

Po nastavení jsou konektory k dispozici v Modeleru.

### Konfigurace Connector secrets

Connector Secrets lze poskytnout jako proměnné prostředí přidáním do souboru `.env` v kořenové složce.

Při startu C8Run s volbou `--docker` přidejte connector secrets do souboru `connector-secrets.txt` ve složce docker-compose.

### Použití Camunda API

Všechna API **nevyžadují ve výchozím nastavení ověřování** v Camunda 8 Run a lze k nim přistupovat bez přihlašovacích údajů či tokenů.

Dostupná API:

- Orchestration Cluster REST API
- Zeebe gRPC

### Zapnutí ověřování a autorizace

Ve výchozím nastavení Camunda 8 Run nastaví ověřování pro webová UI rozhraní (demo/demo), ale všechny API endpointy nevyžadují ověřování. Pro zabezpečení API zapněte autorizaci v application.yaml.

Můžete buď:

- Upravit existující `configuration/application.yaml`, nebo
- Vytvořit nový `application.yaml` ve složce `/c8run` a předat ho při startu pomocí `--config` příznaku:

```yaml
camunda:
  security:
    authentication:
      # Require authentication for API requests
      unprotected-api: false
    authorizations:
      # Enable authorization checks
      enabled: true
```

Spusťte C8Run s touto konfigurací:

```bash
./start.sh --config application.yaml
```

Po zapnutí musí API požadavky obsahovat platné přihlašovací údaje. Například:

```shell
curl --request GET 'http://localhost:8080/v2/topology'  \
  -u demo:demo \
  --header 'Content-Type: application/json' \
  --data-raw '{}'
```

Chcete-li přidat další uživatele (např. admin uživatele), rozšiřte konfiguraci:

```yaml
camunda:
  security:
    initialization:
      users:
        - username: user
          password: user
          name: user
          email: user@example.com
      defaultRoles:
        admin:
          users:
            - user
```

## Vypnutí Camunda 8 Run

Pro vypnutí (ne Docker) Camunda 8 Run a ukončení všech běžících procesů spusťte z adresáře `c8run` následující příkaz:

```bash
./shutdown.sh

# Windows:
# .\c8run.exe stop
```

Pokud jste Camunda 8 Run spustili s Docker `./start.sh --docker`, použijte místo toho následující příkazy:

```bash
# Zastaví kontejnery, ale ponechá existující data
docker compose -f docker-compose-8.8/docker-compose.yaml down

# Zastaví kontejnery a odstraní všechny datové volumes
docker compose -f docker-compose-8.8/docker-compose.yaml down -v
```

Volba `-v` odstraní všechny Docker volumes včetně persistovaných dat, jako jsou uživatelé a instance procesů. Pokud chcete data ponechat pro příští start, `-v` vynechejte.

Pro ověření, že se Camunda 8 Run zastavila, zkontrolujte aktivní kontejnery:

```bash
docker ps
```

## Pokročilé volby

### Zapnutí TLS

TLS lze zapnout dodáním lokálního keystore souboru pomocí argumentu `--keystore` při startu. Camunda 8 Run akeptuje certifikáty z `.jks`.
I když C8Run podporuje TLS, je to určeno pouze pro testování.

### Přístup k metrikám

Metriky jsou v Camunda 8 Run ve výchozím nastavení zapnuté a dostupné na [http://localhost:9600/actuator/prometheus](http://localhost:9600/actuator/prometheus). Další informace viz dokumentace k metrics.

### Spuštění externího Elasticsearch

Ve výchozím nastavení Camunda 8 Run startuje s vestavěným Elasticsearchem.
Pokud chcete použít externí instanci, spusťte Camunda 8 Run bez jejího Elasticsearch a připojte ji k vlastní instanci.

1. Spusťte single-node Elasticsearch kontejner s vypnutým zabezpečením:

   ```bash
   docker run \
       -m 1GB \
       -d \
       --name elasticsearch \
       -p 9200:9200 \
       -p 9300:9300 \
       -e "discovery.type=single-node" \
       -e "xpack.security.enabled=false" \
       elasticsearch:8.15.2
   ```

1. Nakonfigurujte Camunda 8 Run vytvořením souboru `application.yaml`, který ukazuje na váš externí Elasticsearch:

   ```yaml
   camunda:
     data:
       secondary-storage:
         elasticsearch:
           url: "http://127.0.0.1:9200/"
   ```

1. Spusťte Camunda 8 Run s příznakem `--disable-elasticsearch`, aby se nespustila vlastní instance, a dodejte svou konfiguraci:

   ```bash
   ./start.sh --disable-elasticsearch --config application.yaml
   ```

### Proměnné prostředí

Následující pokročilé konfigurační volby lze zadat přes proměnné prostředí:

| Proměnná      | Popis                                                                                                                                                 |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ES_JAVA_OPTS` | Umožňuje přepsat parametry příkazové řádky Javy pro Elasticsearch. Můžete tak zvýšit limity paměti. **Výchozí:** `-Xms1g -Xmx1g`                      |
| `JAVA_OPTS`    | Umožňuje přepsat parametry příkazové řádky Javy pro Camunda.                                                                                          |

