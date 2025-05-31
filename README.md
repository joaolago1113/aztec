# Aztec Node Monitoring & Automation Toolkit

This repository provides a full toolkit for **monitoring**, **alerting**, and **automated response** to issues with an Aztec validator node. It includes:

- High-availability **downtime alarms** that wake you up
- Infrastructure-as-code to **provision Prometheus + Grafana**
- Alerting rules and **Telegram notifications**
- Full EL + CL + Sequencer setup for Aztec

---

## 📁 Repository Structure

| Path | Purpose |
|------|---------|
| [`downtime-alarm/`](./downtime-alarm) | Wake-up alarms for validator downtime or missed attestations on iOS |
| [`monitoring-and-provisioning/`](./monitoring-and-provisioning) | Prometheus + Grafana alerting stack with Telegram integration |
| [`fullstack-deployment/`](./fullstack-deployment) | Geth + Prysm + Aztec sequencer node full install with systemd units |

---

## 🔔 Downtime Wake-Up Alarm ([`downtime-alarm/`](./downtime-alarm))

A simple but reliable health check server powered by **OpenResty + Lua** that UptimeRobot can ping every minute.

- Bypasses "Do Not Disturb" and iOS Sleep mode
- Triggers **max-volume iOS shortcut alarms** when validator is unresponsive or missing attestations
- Monitors via `/health` endpoint

**➡️ Start here** if you just want to get woken up when something breaks.

---

## 📊 Monitoring + Alerting Stack ([`monitoring-and-provisioning/`](./monitoring-and-provisioning))

A Dockerized Grafana + Prometheus + OTLP collector stack for Aztec nodes.

- Preconfigured **Grafana dashboards** + **Telegram alerts**
- Simple `docker run` setup
- Alerts for CPU, memory, disk, block sync, tx pool, block production, more

Includes:
- [`Aztec.yaml`](./monitoring-and-provisioning/alerting/Aztec.yaml): alert rules
- [`Contacts.yaml`](./monitoring-and-provisioning/alerting/Contacts.yaml): contact point (⚠️ requires Telegram token + chat ID)

---

## ⚙️ Full Stack Aztec Node Setup ([`fullstack-deployment/`](./fullstack-deployment))

Instructions to deploy:
- Geth (Execution Layer)
- Prysm (Consensus Layer)
- Aztec sequencer with validator key and startup scripts

Includes:
- `systemd` units for auto-restart
- Environment variable configuration
- Optional monitoring integration

Use this to bootstrap a **resilient Aztec node** on your own Linux machine.

---

## 📌 Getting Started

Choose based on your goal:

| Goal | Start Here |
|------|------------|
| Set up an Aztec validator from scratch | [`fullstack-deployment`](./fullstack-deployment) |
| Full metrics & alerting | [`monitoring-and-provisioning`](./monitoring-and-provisioning) |
| Get notified & wake up if node fails | [`downtime-alarm`](./downtime-alarm) |

---

### ⚠️ Disclaimer

This repository is provided **as-is**, without any warranty or guarantee of reliability, accuracy, or fitness for a particular purpose. Use of this software and its associated configurations is entirely at your own risk.

* You are solely responsible for securing any **API keys**, **bot tokens**, or **private keys** referenced or used by this setup.
* Be aware that incorrect configurations may lead to false alarms, downtime, or exposure of sensitive data.
* Ensure you **verify and sanitize all credentials** before deploying or committing any files publicly.

The author assumes **no liability for loss of funds, uptime, data integrity, or operational failure** arising from the use or misuse of this repository.
