# ✅ Monitoring + Alerts Setup for Aztec

**Stack**: OTLP → Prometheus → Grafana (via `grafana/otel-lgtm`) + Telegram Notifications

---

## 1. 🐳 Launch the Monitoring Stack via Docker

```bash
docker run -d \
  --name aztec-otel \
  -p 4317:4317 \
  -p 4318:4318 \
  -p 4111:3000 \
  -p 9090:9090 \
  -v ~/provisioning/alerting:/otel-lgtm/grafana/conf/provisioning/alerting \
  grafana/otel-lgtm
```

> This launches OpenTelemetry Collector, Prometheus, and Grafana in a single container.

---

## 2. ⚙️ Configure OTEL Export on the Aztec Node

Before starting the Aztec node, export the following environment variables:

```bash
export OTEL_COLLECTOR_ENDPOINT="http://host.docker.internal:4318"
export OTEL_EXPORTER_OTLP_LOGS_ENDPOINT=$OTEL_COLLECTOR_ENDPOINT/v1/logs
export OTEL_EXPORTER_OTLP_METRICS_ENDPOINT=$OTEL_COLLECTOR_ENDPOINT/v1/metrics
export OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=$OTEL_COLLECTOR_ENDPOINT/v1/traces
```

Then start the node:

```bash
aztec start ...
```

> This enables the node to send logs, metrics, and traces to the local OTEL Collector.

---

## 3. 📁 Provisioning Files for Grafana Alerting

Ensure the following files exist in:

```bash
~/provisioning/alerting/
```

### 🔹 `Aztec.yaml` – Alert Rule

Download from:
[Aztec.yaml](https://github.com/joaolago1113/aztec/blob/main/monitoring-and-provisioning/alerting/Aztec.yaml)

Place in:

```bash
~/provisioning/alerting/Aztec.yaml
```

---

### 🔹 `Contacts.yaml` – Telegram Contact Point

Download from:
[Contacts.yaml](https://github.com/joaolago1113/aztec/blob/main/monitoring-and-provisioning/alerting/Contacts.yaml)

**Edit it** to set your own bot and group chat:

```yaml
bottoken: <BOT_TOKEN>     # Replace with your Telegram bot token
chatid: "<CHAT_ID>"       # Replace with your Telegram group chat ID
```

Save to:

```bash
~/provisioning/alerting/Contacts.yaml
```

---

## 4. 📲 Create Telegram Bot and Retrieve Chat ID

### A. Create a Bot

1. Open Telegram, search `@BotFather`
2. Send `/start` then `/newbot`
3. Provide a bot name and username (must end with `bot`)
4. Copy the **API token**

→ Use this as `<BOT_TOKEN>`

---

### B. Get the Chat ID

1. Create a Telegram **group**
2. Add the bot to the group
3. Add `@JsonViewBot` to the same group
4. It will post the **chat ID** and exit

→ Use this as `<CHAT_ID>`

---

## 5. 🔁 Restart the Monitoring Container

After editing and placing the provisioning files:

```bash
docker restart aztec-otel
```

Grafana will auto-load the Telegram contact point and the alert rule.

---

## 6. 📝 Access via SSH Tunnel (Remote Server)

If running the validator on a remote machine, forward ports to access Grafana and Prometheus locally:

```bash
ssh -v \
  -L 4111:localhost:4111 \
  -L 9090:localhost:9090 \
  -i ~/.ssh/KEY \
  -N -f USER@IP
```
> Grafana UI: `http://localhost:4111`
> Prometheus: `http://localhost:9090`

* Replace `KEY` with your SSH key filename if you're accessing this way
* Replace `USER@IP` with the remote server's SSH username and IP

Then open:

* Grafana: [http://localhost:4111](http://localhost:4111)
* Prometheus: [http://localhost:9090](http://localhost:9090)

---

## 7. ✅ Verification

* Grafana: [http://localhost:4111](http://localhost:4111)
* Telegram: Receive alerts when thresholds are triggered (e.g., no new blocks)
* Contact points: `Alerting → Contact points`
* Alert rules: `Alerting → Alert Rules`:

<img width="1201" alt="image" src="https://github.com/user-attachments/assets/6e4c5079-dc3a-492f-bdd5-3ace836e0895" />

---

## 8. 🔽 Add the Aztec Validator Dashboard

Import this Aztec validator dashboard:

1. Open Grafana at `http://localhost:4111`
2. Go to `Dashboards → Import`
  <img width="1519" alt="image" src="https://github.com/user-attachments/assets/90b2e55b-9b3a-4227-8b86-8a6cb9774bf1" />

4. Under "Import dashboard", enter the dashboard ID:
```
23054
```
  <img width="1519" alt="image" src="https://github.com/user-attachments/assets/b2be0a12-edc7-4889-8321-6b4902b0494a" />

5. Press **Load**
6. Select your Prometheus data source
7. Under Folder select **Aztec**
8. Click **Import**
  <img width="1524" alt="image" src="https://github.com/user-attachments/assets/b22afc0e-cae7-46eb-a015-d2053a28dc69" />

The dashboard will now be visible under `Dashboards → Browse -> Aztec`

Dashboard link: [https://grafana.com/grafana/dashboards/23054-aztec-validator/](https://grafana.com/grafana/dashboards/23054-aztec-validator/)

---

## 📌 Recap

| Step | Purpose                                            |
| ---- | -------------------------------------------------- |
| 1    | Run Grafana, Prometheus, and OTEL stack via Docker |
| 2    | Configure OTEL exports in the Aztec node           |
| 3    | Load provisioning files for alerts and contacts    |
| 4    | Set up Telegram bot and retrieve chat ID           |
| 5    | Restart the container to activate configuration    |
| 6    | Verify dashboard and Telegram alerts               |
| 7    | Import Grafana dashboard for validator metrics     |
| 8    | (Optional) Port-forward remote access via SSH      |

---

Monitoring and alerting are now operational.
