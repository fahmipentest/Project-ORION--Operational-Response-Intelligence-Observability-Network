[README.md](https://github.com/user-attachments/files/34679783/README.md)

<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://wazuh.com/brand-assets/Wazuh-Logo-dark-bg.svg">
  <img src="https://wazuh.com/brand-assets/Wazuh-Logo.svg" alt="Wazuh logo" width="260">
</picture>

<br><br>

# 🛰️ PROJECT ORION

### *Operational Response, Intelligence & Observability Network*

**Lab SOC L1 pribadi berbasis Wazuh SIEM — 4 Hari, Full Hands-On**

<br>

![Wazuh](https://img.shields.io/badge/Wazuh-4.14.x-005571?style=for-the-badge&logo=wazuh&logoColor=white)
![Status](https://img.shields.io/badge/Status-In%20Progress-1E90FF?style=for-the-badge)
![Duration](https://img.shields.io/badge/Duration-4%20Days-1E90FF?style=for-the-badge)
![Level](https://img.shields.io/badge/Level-SOC%20L1-1E90FF?style=for-the-badge)
![License](https://img.shields.io/badge/License-Educational%20Use-1E90FF?style=for-the-badge)

![Ubuntu](https://img.shields.io/badge/Ubuntu%20Server-E95420?style=flat-square&logo=ubuntu&logoColor=white)
![Windows](https://img.shields.io/badge/Windows%2011%20Enterprise-0078D6?style=flat-square&logo=windows11&logoColor=white)
![Kali](https://img.shields.io/badge/Kali%20Linux-557C94?style=flat-square&logo=kalilinux&logoColor=white)
![VirtualBox](https://img.shields.io/badge/VirtualBox-183A61?style=flat-square&logo=virtualbox&logoColor=white)
![Suricata](https://img.shields.io/badge/Suricata%20IDS-CC0000?style=flat-square)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)

</div>

<br>

> [!NOTE]
> **PROJECT ORION** (*Operational Response, Intelligence & Observability Network*) adalah lab latihan SOC L1 yang dibangun di atas **Wazuh SIEM**, menggunakan tools gratis/open-source. Tutorial ini berdasarkan **Course Syllabus resmi "Wazuh for Security Engineers (4-Day)"** dan dokumen **"Recommended Prerequisites"**, lalu dipersonalisasi untuk topologi lab milik `fahmipentest`.

## 🧩 Komponen & Teknologi

| Kategori | Teknologi |
| --- | --- |
| **SIEM Core** | Wazuh (Manager, Indexer, Dashboard) |
| **Endpoint Telemetry** | Sysmon (Windows), Microsoft Defender |
| **Network Detection** | Suricata IDS |
| **Threat Intelligence** | VirusTotal, AbuseIPDB, MISP |
| **Alerting** | Telegram, Slack |
| **Identity** | Active Directory |
| **Offensive Simulation** | Kali Linux (Hydra, Nmap) |

## 🖥️ Topologi Lab

| VM | Peran | Hostname | IP | Spek Minimum |
| --- | --- | --- | --- | --- |
| 🟦 **Wazuh OVA** | Server all-in-one (manager + indexer + dashboard) | `wazuh-user` | `192.168.56.123` | 4 vCPU, 8 GB RAM |
| 🐧 **Ubuntu Server 26.04** | Agent Linux | `orion` | `192.168.56.125` | 2 vCPU, 2 GB RAM |
| 🪟 **Windows 11 Enterprise** | Agent Windows | `Fahmi` | `192.168.56.124` | 2 vCPU, 4 GB RAM |
| 🐉 **Kali Linux** | Penyerang (hydra, nmap) | `fahmipentest` | `192.168.56.118` | 1 vCPU, 2 GB RAM |

> [!WARNING]
> Semua simulasi serangan (brute force, encoded command, dsb.) dalam dokumen ini **hanya untuk dijalankan di lab pribadi yang terisolasi** (Host-only / NAT Network). Jangan arahkan ke sistem yang bukan milik Anda sendiri.

## 📑 Daftar Isi


- [📖 0. Cara Memakai Panduan Ini](#0-cara-memakai-panduan-ini)
- [🧰 TAHAP 0: Persiapan Lab](#tahap-0-persiapan-lab)
- [1️⃣ TAHAP 1: DAY 1 (Setup dan Agents)](#tahap-1-day-1-setup-dan-agents)
- [2️⃣ TAHAP 2: DAY 2 (Log Analysis dan Ruleset)](#tahap-2-day-2-log-analysis-dan-ruleset)
- [3️⃣ TAHAP 3: DAY 3 (CDB Lists, Indexer, FIM, Inventory, Rootkit)](#tahap-3-day-3-cdb-lists-indexer-fim-inventory-rootkit)
- [4️⃣ TAHAP 4: DAY 4 (Active Response, Integrasi, SCA, Cloud, Sysmon, Docker)](#tahap-4-day-4-active-response-integrasi-sca-cloud-sysmon-docker)
- [✅ Ringkasan Checklist Laboratory Exercises (sesuai Course Syllabus)](#ringkasan-checklist-laboratory-exercises-sesuai-course-syllabus)
- [📝 Catatan Penutup](#catatan-penutup)

---

<a id="0-cara-memakai-panduan-ini"></a>
<details>
<summary><b>📖 0. Cara Memakai Panduan Ini</b></summary>


- Struktur: **Tahap 0 (Persiapan Lab)** lalu **Day 1 sampai Day 4**. Setiap lab memakai ID yang sama dengan syllabus (1a sampai 4e).
- Tag lokasi perintah: **[WZ]** = Wazuh OVA, **[UB]** = Ubuntu agent, **[WIN]** = Windows 11 agent (PowerShell **Administrator**), **[ATK]** = VM penyerang (opsional tapi disarankan), **[WEB]** = browser (Wazuh Dashboard).
- Setiap lab punya blok **Checklist**. Centang (☐ menjadi ☑) **hanya setelah bukti verifikasi di blok itu terpenuhi**, sesuai aturan self-assessment Wazuh: tandai Yes hanya jika bisa dilakukan tanpa bantuan.
- Semua simulasi serangan (brute force, user mencurigakan, dll.) **hanya dijalankan di lab pribadi yang terisolasi** (Host-only atau NAT Network).
- Ganti IP contoh dengan IP lab Anda sendiri.

### Topologi dan IP contoh

| VM | Peran | Hostname | IP contoh | Spek minimum |
| --- | --- | --- | --- | --- |
| Wazuh OVA | Server all-in-one (manager + indexer + dashboard) | wazuh-server | 192.168.56.123 | 4 vCPU, 8 GB RAM |
| Ubuntu Server 24.04 / 22.04 LTS | Agent Linux | orion | 192.168.56.125 | 2 vCPU, 2 GB RAM |
| Windows 11 Enterprise (evaluation) | Agent Windows | Fahmi | 192.168.56.124 | 2 vCPU, 4 GB RAM |
| Kali / Debian | Penyerang (hydra, nmap) | attacker | 192.168.56.118 | 1 vCPU, 2 GB RAM |

**Pilihan Linux agent:** pakai **Ubuntu Server** (ringan, SSH bawaan, apt). Linux Mint (basis Ubuntu) juga bisa dengan perintah yang sama, tetapi lebih berat karena desktop.

### Port yang dipakai

| Port | Fungsi |
| --- | --- |
| 1514/tcp | Agent mengirim event ke manager |
| 1515/tcp | Enrollment (registrasi agent) |
| 55000/tcp | Wazuh API |
| 443/tcp | Wazuh Dashboard |
| 9200/tcp | Wazuh Indexer |
| 514/udp | Syslog dari perangkat jaringan (Day 2) |

---


</details>

<a id="tahap-0-persiapan-lab"></a>
<details open>
<summary><b>🧰 TAHAP 0: Persiapan Lab</b></summary>


### 0.1 Wazuh OVA [WZ]

1. Unduh OVA dari halaman resmi: documentation.wazuh.com, menu Deployment options, Virtual Machine (OVA). Pilih versi 4.14.x yang paling baru.
2. Import ke VirtualBox/VMware: **File, Import Appliance**. Atur 4 vCPU dan 8 GB RAM. Network adapter: **NAT Network** atau **Host-only** (semua VM harus satu segmen).
3. Boot, login konsol: user `wazuh-user`, password `wazuh`.
4. Cek IP dan versi:

```bash
sudo -i
ip -4 a
/var/ossec/bin/wazuh-control info
timedatectl
```

5. Dashboard: buka `https://192.168.56.123` di browser, login `admin` / `admin`. Abaikan peringatan sertifikat.

> [!WARNING]
> Catatan: OVA untuk testing/lab (bukan produksi). Jika IP berubah setelah reboot, pakai DHCP reservation atau perbarui IP di agent.


### 0.2 Ubuntu Server agent [UB]

1. Instal Ubuntu Server, centang **Install OpenSSH server**. User contoh: `orion`. Hostname: `orion`.
2. Siapkan paket dasar:

```bash
sudo apt update && sudo apt -y upgrade
sudo apt -y install curl gnupg apt-transport-https net-tools iptables auditd
sudo systemctl enable --now ssh
sudo timedatectl set-timezone Asia/Jakarta
```

3. Buat user target untuk simulasi brute force (password sengaja kuat dan tidak ada di wordlist):

```bash
sudo adduser labuser
```

### 0.3 Windows 11 Enterprise agent [WIN]

1. Instal Win11 Enterprise evaluation. Nama PC: `Fahmi`.
2. Aktifkan RDP (PowerShell Administrator):

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name 'fDenyTSConnections' -Value 0
Enable-NetFirewallRule -DisplayGroup 'Remote Desktop'
Get-Service TermService
```

3. Dari host, tes `mstsc /v:192.168.56.124`.

### 0.4 VM Attacker [ATK]

```bash
sudo apt update && sudo apt -y install hydra nmap
```

### 0.5 Tes konektivitas (sesuai prerequisite: network & SSH/RDP)

```bash
# dari ATK atau Ubuntu
ping -c 2 192.168.56.123
nc -zv 192.168.56.123 443
ssh orion@192.168.56.125
# dari Wazuh server
ping -c 2 192.168.56.125
ss -tlnp | head
```

### 0.6 Snapshot

Buat snapshot **Baseline-Clean** untuk semua VM sebelum mulai (rollback konseptual sesuai bagian 3.5 prerequisite).

### Checklist Tahap 0

Centang jika:

> [!CAUTION]
> **Checklist Tahap 0** — centang hanya jika:
>
> - [ ] 🔴 Dashboard terbuka di browser dan login `admin` berhasil (bukti: halaman Overview tampil)
> - [ ] 🔴 `ping` dan `ssh` dari Ubuntu/ATK ke Wazuh/Ubuntu berhasil (bukti: balasan ping 0% loss)
> - [ ] 🔴 RDP ke Win11 berhasil dan PowerShell Administrator terbuka
> - [ ] 🔴 Waktu semua VM sinkron (selisih kurang dari 1 menit) agar timestamp alert tidak membingungkan
> - [ ] 🔴 Snapshot Baseline-Clean sudah dibuat di keempat VM

<img width="1902" height="967" alt="Screenshot 2026-11-11 170454" src="https://github.com/user-attachments/assets/75313dff-de39-4e75-a717-da3c84d00246" />
<img width="1651" height="651" alt="Screenshot 2026-11-11 170123" src="https://github.com/user-attachments/assets/7e0f2e0d-c543-4bfd-824e-1f280672d494" />
<img width="508" height="131" alt="Screenshot 2026-11-11 163023" src="https://github.com/user-attachments/assets/279827d6-4b25-4452-8109-ccd76475ed32" />
<img width="872" height="610" alt="Screenshot 2026-11-11 162506" src="https://github.com/user-attachments/assets/902b3d99-7837-42d4-aee1-02cfa93a2964" />
<img width="865" height="605" alt="Screenshot 2026-11-11 162532" src="https://github.com/user-attachments/assets/4022a010-c8e3-460b-a920-0b0c76ec40c4" />
<img width="552" height="243" alt="Screenshot 2026-11-11 162935" src="https://github.com/user-attachments/assets/e3a84ec6-0632-4051-b74d-83b6a063bb4f" />






---


</details>

<a id="tahap-1-day-1-setup-dan-agents"></a>
<details>
<summary><b>1️⃣ TAHAP 1: DAY 1 (Setup dan Agents)</b></summary>


Cakupan syllabus: Arsitektur, Cluster, Konfigurasi, Registrasi Agent, Dashboard, Upgrade Agent, Centralized Config. Lab 1a sampai 1g.

### 1a. Prepare Wazuh Server Components [WZ]

> [!TIP]
> **Tujuan:** memahami komponen, alert level, autentikasi enrollment, dan konektivitas API.


> [!NOTE]
> **Konsep singkat:** Agent mengirim event (1514) ke **Wazuh server** (analisis: decoder dan rule). Alert ditulis ke `alerts.json`, diteruskan **Filebeat** ke **Wazuh Indexer** (9200), lalu ditampilkan **Wazuh Dashboard**. OVA = all-in-one (satu node). Pada multi-node, **master** mengelola agent/konfigurasi dan **worker** memproses event lalu sinkron ke master.


**Langkah:**

1. Cek semua layanan:

```bash
sudo -i
/var/ossec/bin/wazuh-control status
systemctl status wazuh-manager wazuh-indexer wazuh-dashboard filebeat --no-pager | grep -E 'Active|●'
curl -k -u admin:admin https://localhost:9200
/var/ossec/bin/cluster_control -l   # pada OVA cluster nonaktif, ini normal
```

2. Lihat contoh alert level (0 sampai 15). Level 0 diabaikan, 3 sampai 5 informasional/error biasa, 7 sampai 9 perlu perhatian, 10 sampai 12 serangan/kegagalan kritis, 13 sampai 15 serangan serius:

```bash
tail -n 3 /var/ossec/logs/alerts/alerts.json
grep -o '"level":[0-9]*' /var/ossec/logs/alerts/alerts.json | sort | uniq -c
```

3. Aktifkan **autentikasi enrollment dengan password**:

```bash
echo 'LabPass123!' > /var/ossec/etc/authd.pass
chmod 640 /var/ossec/etc/authd.pass
chown root:wazuh /var/ossec/etc/authd.pass
sed -i 's#<use_password>no</use_password>#<use_password>yes</use_password>#' /var/ossec/etc/ossec.conf
grep -n 'use_password' /var/ossec/etc/ossec.conf
systemctl restart wazuh-manager
```

4. Tes **konektivitas API** (user default OVA: `wazuh-wui`; jika password berbeda, lihat dokumentasi OVA versi Anda):

```bash
TOKEN=$(curl -s -u wazuh-wui:'MyS3cr37P450r.*-' -k -X POST 'https://localhost:55000/security/user/authenticate?raw=true')
echo $TOKEN | cut -c1-30
curl -k -X GET 'https://localhost:55000/?pretty=true' -H "Authorization: Bearer $TOKEN"
```

**Verifikasi:** `wazuh-control status` menampilkan wazuh-analysisd, wazuh-remoted, wazuh-apid, dll. **running**; API mengembalikan JSON dengan `api_version`.

**Checklist 1a**

> [!CAUTION]
> **Checklist 1a** — centang hanya jika:
>
> - [ ] 🔴 Semua layanan manager/indexer/dashboard/filebeat Active (running)
> - [ ] 🔴 `use_password` bernilai yes dan `authd.pass` terisi, manager sudah restart
> - [ ] 🔴 Token API berhasil didapat dan endpoint `/` mengembalikan JSON
> - [ ] 🔴 Anda bisa menjelaskan arti level 0 sampai 15 dan jalur data agent, analysisd, filebeat, indexer, dashboard


### 1b. Navigate the Dashboard [WEB]

**Langkah:** login `https://192.168.56.123`, lalu telusuri menu berikut dan catat fungsinya:

1. **Overview**: ringkasan alert, agent aktif.
2. **Endpoints, Agents**: status agent (belum ada, akan muncul di 1c).
3. **Threat Hunting / Events**: tabel dan filter alert (coba filter `rule.level >= 7`).
4. **Server management**: Rules, Decoders, CDB Lists, Groups, Status, Settings (config editor), **Dev Tools** (Wazuh API console).
5. **Indexer management**: Index Management, Dev Tools (query indexer), dipakai di Day 3.
6. Coba satu query di Server management, Dev Tools: `GET /manager/status`.

**Checklist 1b**

> [!CAUTION]
> **Checklist 1b** — centang hanya jika:
>
> - [ ] 🔴 Bisa menemukan Rules, Decoders, Groups, Dev Tools, dan Settings tanpa mencari lama
> - [ ] 🔴 Query `GET /manager/status` di Dev Tools memberi respons


### 1c. Register linux-agent [UB]

> [!TIP]
> **Tujuan:** instal agent Linux dan self-enrollment memakai password. Untuk lab 1f (upgrade), kita **sengaja memasang versi satu minor lebih lama** dari manager (agent tidak boleh lebih baru dari manager).


**Langkah:**

1. Tambah repo Wazuh:

```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && sudo chmod 644 /usr/share/keyrings/wazuh.gpg
echo 'deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main' | sudo tee /etc/apt/sources.list.d/wazuh.list
sudo apt update
apt-cache madison wazuh-agent | head
```

2. Pilih versi lama dari daftar (contoh `4.13.1-1`; sesuaikan dengan versi yang tampil, satu minor di bawah manager), lalu instal dengan variabel enrollment:

```bash
sudo WAZUH_MANAGER='192.168.56.123' WAZUH_AGENT_NAME='orion' WAZUH_REGISTRATION_PASSWORD='LabPass123!' apt-get install -y wazuh-agent=4.13.1-1
sudo apt-mark hold wazuh-agent
sudo systemctl daemon-reload
sudo systemctl enable --now wazuh-agent
```

3. Metode registrasi lain (pahami perbedaannya): manual lewat `agent-auth`:

```bash
# contoh saja, tidak perlu dijalankan ulang
sudo /var/ossec/bin/agent-auth -m 192.168.56.123 -P 'LabPass123!' -A orion
```

4. Verifikasi:

```bash
sudo systemctl status wazuh-agent --no-pager
sudo tail -n 20 /var/ossec/logs/ossec.log      # cari 'Connected to the server'
```

```bash
# [WZ]
/var/ossec/bin/agent_control -l
```

**Checklist 1c**

> [!CAUTION]
> **Checklist 1c** — centang hanya jika:
>
> - [ ] 🔴 `ossec.log` agent memuat 'Connected to the server'
> - [ ] 🔴 `agent_control -l` di manager menampilkan `orion` berstatus **Active** (ID 001)
> - [ ] 🔴 Agent tampil Active di Dashboard, Agents
> - [ ] 🔴 Anda bisa menyebut 2 metode registrasi (variabel saat instal dan `agent-auth`)


### 1d. Install Windows Agent [WIN]

Ganti versi MSI dengan versi **manager Anda** (cek di 0.1).

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.4-1.msi -OutFile $env:tmp\wazuh-agent.msi
msiexec.exe /i $env:tmp\wazuh-agent.msi /q WAZUH_MANAGER='192.168.56.123' WAZUH_AGENT_NAME='Fahmi' WAZUH_REGISTRATION_PASSWORD='LabPass123!'
NET START WazuhSvc
Get-Service WazuhSvc
Get-Content 'C:\Program Files (x86)\ossec-agent\ossec.log' -Tail 20
```

**Checklist 1d**

> [!CAUTION]
> **Checklist 1d** — centang hanya jika:
>
> - [ ] 🔴 Service `WazuhSvc` berstatus Running
> - [ ] 🔴 `ossec.log` Windows memuat 'Connected to the server'
> - [ ] 🔴 `Fahmi` tampil Active di Dashboard (ID 002)


### 1e. Register via Dashboard [WEB]

1. Agents, **Deploy new agent**. Pilih OS, isi alamat server `192.168.56.123`, nama agent, dan (opsional) group.
2. Bandingkan perintah yang dihasilkan wizard dengan perintah manual di 1c/1d. Pahami variabel `WAZUH_MANAGER`, `WAZUH_AGENT_NAME`, `WAZUH_AGENT_GROUP`.
3. (Opsional) jalankan perintah wizard di VM ketiga (mis. ATK) untuk menambah agent.

**Checklist 1e**

> [!CAUTION]
> **Checklist 1e** — centang hanya jika:
>
> - [ ] 🔴 Anda sudah menghasilkan perintah instal dari wizard untuk Linux **dan** Windows
> - [ ] 🔴 Anda bisa menjelaskan perbedaan perintah wizard vs manual


### 1f. Agent Upgrades [WZ]

Prasyarat: manager memiliki akses internet (mengunduh paket WPK).

```bash
/var/ossec/bin/agent_upgrade -l            # daftar agent yang outdated
/var/ossec/bin/agent_upgrade -a 001        # upgrade orion via manager
```

Cara via API:

```bash
TOKEN=$(curl -s -u wazuh-wui:'MyS3cr37P450r.*-' -k -X POST 'https://localhost:55000/security/user/authenticate?raw=true')
curl -k -X PUT 'https://localhost:55000/agents/upgrade?agents_list=001' -H "Authorization: Bearer $TOKEN"
curl -k -X GET 'https://localhost:55000/agents?agents_list=001&select=version,status' -H "Authorization: Bearer $TOKEN"
/var/ossec/bin/agent_control -i 001
```

**Verifikasi:** versi agent 001 naik ke versi manager dan status kembali Active (tunggu 1 sampai 3 menit).

**Checklist 1f**

> [!CAUTION]
> **Checklist 1f** — centang hanya jika:
>
> - [ ] 🔴 `agent_upgrade -l` sempat menampilkan agent 001 sebagai outdated
> - [ ] 🔴 Setelah upgrade, `agent_control -i 001` menampilkan versi baru dan status Active
> - [ ] 🔴 Anda bisa menjelaskan kenapa agent tidak boleh lebih baru dari manager


### 1g. Centralized Configuration [WZ]

> [!TIP]
> **Tujuan:** memakai **agent groups** dan `agent.conf` untuk mendorong konfigurasi dari manager ke banyak agent. `ossec.conf` = konfigurasi lokal; `agent.conf` = konfigurasi terpusat per group.


1. Buat group dan masukkan agent:

```bash
/var/ossec/bin/agent_groups -a -g linux-servers -q
/var/ossec/bin/agent_groups -a -g windows-workstations -q
/var/ossec/bin/agent_groups -a -i 001 -g linux-servers -q
/var/ossec/bin/agent_groups -a -i 002 -g windows-workstations -q
/var/ossec/bin/agent_groups -l
```

2. Isi `agent.conf` group Linux (file ini akan diperluas di Day 2 sampai Day 3):

```bash
vi /var/ossec/etc/shared/linux-servers/agent.conf
```

```xml
<agent_config os="Linux">
  <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/myapp.log</location>
  </localfile>
</agent_config>
```

3. Validasi sintaks dan cek propagasi:

```bash
/var/ossec/bin/verify-agent-conf
/var/ossec/bin/agent_groups -c -g linux-servers     # cek sinkronisasi
```

```bash
# [UB] buat file log dan lihat konfigurasi tiba
sudo touch /var/log/myapp.log && sudo chmod 644 /var/log/myapp.log
ls -l /var/ossec/etc/shared/
sudo tail -n 20 /var/ossec/logs/ossec.log | grep -i myapp
```

4. Opsional: kelola group lewat API:

```bash
curl -k -X PUT 'https://localhost:55000/agents/001/group/linux-servers' -H "Authorization: Bearer $TOKEN"
```

**Checklist 1g**

> [!CAUTION]
> **Checklist 1g** — centang hanya jika:
>
> - [ ] 🔴 `agent_groups -l` menampilkan agent 001 di linux-servers dan 002 di windows-workstations
> - [ ] 🔴 `verify-agent-conf` tidak menampilkan error
> - [ ] 🔴 `ossec.log` di Ubuntu menunjukkan `/var/log/myapp.log` mulai dipantau (konfigurasi terpusat sampai)
> - [ ] 🔴 Anda bisa menjelaskan beda `ossec.conf` dan `agent.conf`


### Checklist Akhir DAY 1

Centang jika seluruh lab 1a sampai 1g selesai **dan**:

> [!CAUTION]
> **Checklist Akhir DAY 1** — centang hanya jika:
>
> - [ ] 🔴 2 agent (Linux dan Windows) berstatus Active di Dashboard
> - [ ] 🔴 Enrollment memakai password aktif
> - [ ] 🔴 Agent Linux sudah diupgrade lewat manager
> - [ ] 🔴 Group `linux-servers` dan `windows-workstations` berfungsi
> - [ ] 🔴 Snapshot **End-Day1** dibuat di semua VM


</details>

<a id="tahap-2-day-2-log-analysis-dan-ruleset"></a>
<details>
<summary><b>2️⃣ TAHAP 2: DAY 2 (Log Analysis dan Ruleset)</b></summary>


Cakupan syllabus: Log Analysis, Wazuh Ruleset, Decoders and Rules. Lab 2a sampai 2f.

### 2a. Brute Force Attack [ATK] lalu [WZ]

> [!TIP]
> **Tujuan:** menghasilkan serangan nyata di lab agar ada data untuk dianalisis.


1. **[WZ]** Pastikan modul SSHD Wazuh default aktif (bawaan). Opsional, perketat agar cepat terdeteksi di `/etc/ssh/sshd_config` pada **[UB]**: `MaxAuthTries 3` lalu `sudo systemctl restart ssh`.
2. **[ATK]** Buat wordlist kecil dan jalankan hydra ke user `labuser` (password-nya sengaja tidak ada di wordlist supaya semua percobaan gagal, menghasilkan alert "multiple failed logins"):

```bash
cat <<'EOF' > pass.txt
password
123456
admin123
letmein
qwerty123
EOF
hydra -l labuser -P pass.txt ssh://192.168.56.125 -t 4 -f
```

3. **[UB]** Lihat log mentah penyebab alert:

```bash
sudo tail -n 30 /var/log/auth.log | grep -i 'labuser\|Failed'
```

**Checklist 2a**

> [!CAUTION]
> **Checklist 2a** — centang hanya jika:
>
> - [ ] 🔴 Hydra menghasilkan beberapa baris "Failed password" di `/var/log/auth.log` Ubuntu
> - [ ] 🔴 Anda memahami ini simulasi, dijalankan hanya di jaringan lab terisolasi


### 2b. Assess Several Malicious Alerts [WEB]

1. Dashboard, **Threat Hunting**, filter `agent.name: orion` dan `rule.description: *ssh*`.
2. Temukan alert dengan level tinggi (contoh rule 5712/5720-an: "sshd: brute force being attempted").
3. Buka detail alert, catat: `rule.id`, `rule.level`, `rule.description`, `data.srcip`, `full_log`.
4. Bandingkan dengan alert level rendah (percobaan gagal tunggal, biasanya level 5) vs alert terkorelasi (banyak gagal dalam rentang waktu, level lebih tinggi, misal 10).

**Checklist 2b**

> [!CAUTION]
> **Checklist 2b** — centang hanya jika:
>
> - [ ] 🔴 Anda menemukan minimal satu alert "brute force" dengan level >= 10
> - [ ] 🔴 Anda bisa menyebutkan `rule.id` dan `data.srcip` dari alert tersebut


### 2c. Looking Up Rules [WZ] dan [WEB]

1. Dari `rule.id` yang dicatat di 2b, cari definisinya:

```bash
grep -rn 'id="5712"\|id="5720"' /var/ossec/ruleset/rules/0095-sshd_rules.xml
```

2. Baca atribut: `frequency`, `timeframe`, `if_matched_sid` (rule hierarki, parent-child).
3. Di Dashboard, **Server management, Rules**, cari rule yang sama, lihat **group**, **compliance mapping** (PCI_DSS, GDPR, dsb.), dan **MITRE ATT&CK** technique yang terpetakan.
4. Jelaskan dengan kata sendiri: atomic rule (event tunggal) vs composite rule (frequency + timeframe, mengaitkan banyak event).

**Checklist 2c**

> [!CAUTION]
> **Checklist 2c** — centang hanya jika:
>
> - [ ] 🔴 Anda menunjukkan XML rule brute force dan menjelaskan `frequency`/`timeframe`-nya
> - [ ] 🔴 Anda menemukan MITRE technique dan compliance tag rule tersebut di Dashboard


### 2d. Write a Custom Decoder [WZ]

> [!TIP]
> **Tujuan:** decoder memecah `full_log` menjadi field terstruktur sebelum rule mengevaluasinya.


1. Buat log aplikasi contoh:

```bash
echo '2026-11-05 10:00:01 myapp: ERROR user=admin action=login status=denied' >> /var/log/myapp.log
```

(Catatan: `/var/log/myapp.log` sudah dipantau lewat `agent.conf` dari lab 1g pada agent Ubuntu; untuk latihan decoder cepat, kita buat juga file lokal di manager.)

2. Buat decoder custom:

```bash
vi /var/ossec/etc/decoders/local_decoder.xml
```

```xml
<decoder name="myapp">
  <prematch>^\S+ \S+ myapp:</prematch>
</decoder>

<decoder name="myapp-fields">
  <parent>myapp</parent>
  <regex>ERROR user=(\S+) action=(\S+) status=(\S+)</regex>
  <order>user, action, status</order>
</decoder>
```

3. Uji dengan `wazuh-logtest`:

```bash
echo '2026-11-05 10:00:01 myapp: ERROR user=admin action=login status=denied' | /var/ossec/bin/wazuh-logtest
```

4. Verifikasi output menunjukkan `decoder: myapp-fields` dan field `user`, `action`, `status` terisi.

**Checklist 2d**

> [!CAUTION]
> **Checklist 2d** — centang hanya jika:
>
> - [ ] 🔴 `wazuh-logtest` menampilkan decoder `myapp-fields` cocok dengan log contoh
> - [ ] 🔴 Field `user=admin`, `action=login`, `status=denied` terbaca benar di output


### 2e. Write a Sibling Decoder [WZ]

> [!TIP]
> **Tujuan:** decoder "sibling" menangani variasi format log yang sama tanpa mengubah decoder utama (decoder chaining).


1. Tambahkan varian format log (`result=` bukan `status=`):

```xml
<decoder name="myapp-fields-v2">
  <parent>myapp</parent>
  <regex>ERROR user=(\S+) action=(\S+) result=(\S+)</regex>
  <order>user, action, status</order>
</decoder>
```

2. Uji kedua varian:

```bash
echo '2026-11-05 10:00:01 myapp: ERROR user=root action=delete result=denied' | /var/ossec/bin/wazuh-logtest
```

3. Buat rule dasar yang mengacu field `status`:

```bash
vi /var/ossec/etc/rules/local_rules.xml
```

```xml
<group name="myapp,">
  <rule id="100010" level="5">
    <decoded_as>myapp-fields</decoded_as>
    <field name="status">denied</field>
    <description>MyApp: action denied for user $(user)</description>
  </rule>
</group>
```

4. Restart dan tes ulang:

```bash
systemctl restart wazuh-manager
echo '2026-11-05 10:00:01 myapp: ERROR user=admin action=login status=denied' | /var/ossec/bin/wazuh-logtest
```

**Checklist 2e**

> [!CAUTION]
> **Checklist 2e** — centang hanya jika:
>
> - [ ] 🔴 Kedua varian log (`status=` dan `result=`) berhasil di-decode ke field yang sama (`status`)
> - [ ] 🔴 Rule 100010 muncul sebagai match di `wazuh-logtest` dengan deskripsi berisi nama user


### 2f. Wazuh's Dynamic JSON Decoder [UB] dan [WZ]

> [!TIP]
> **Tujuan:** Wazuh otomatis mem-parsing event JSON tanpa decoder manual per field.


1. **[UB]** Simulasikan log JSON (nanti dipakai nyata oleh Suricata di 2g):

```bash
echo '{"timestamp":"2026-11-05T10:05:00","event_type":"alert","src_ip":"192.168.56.118","dest_ip":"192.168.56.125","alert":{"signature":"Test JSON Alert"}}' >> /var/log/myapp.log
```

2. **[WZ]** Tes dengan `wazuh-logtest`, perhatikan Wazuh otomatis mengenali ini JSON dan field `src_ip`, `dest_ip`, `alert.signature` langsung tersedia tanpa decoder regex tambahan:

```bash
echo '{"timestamp":"2026-11-05T10:05:00","event_type":"alert","src_ip":"192.168.56.118","dest_ip":"192.168.56.125","alert":{"signature":"Test JSON Alert"}}' | /var/ossec/bin/wazuh-logtest
```

**Checklist 2f**

> [!CAUTION]
> **Checklist 2f** — centang hanya jika:
>
> - [ ] 🔴 Output `wazuh-logtest` menunjukkan field JSON (`src_ip`, `alert.signature`) terbaca otomatis
> - [ ] 🔴 Anda bisa menjelaskan kapan decoder manual tetap diperlukan (log non-JSON/non-standar) vs kapan dynamic decoder cukup (JSON)


### Deploy Suricata (bagian dari modul Decoders and Rules) [UB]

```bash
sudo apt -y install suricata
sudo suricata-update
sudo systemctl enable --now suricata
sudo tail -f /var/log/suricata/eve.json | head -n 5
```

Tambahkan localfile JSON Suricata via `agent.conf` group `linux-servers` **[WZ]**:

```xml
<agent_config os="Linux">
  <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/myapp.log</location>
  </localfile>
  <localfile>
    <log_format>json</log_format>
    <location>/var/log/suricata/eve.json</location>
  </localfile>
</agent_config>
```

Verifikasi di Dashboard: filter `agent.name: orion` dan `rule.groups: suricata` muncul setelah ada traffic (mis. `curl` ke situs test atau scan nmap dari ATK).

**Checklist Suricata**

> [!CAUTION]
> **Checklist Suricata** — centang hanya jika:
>
> - [ ] 🔴 `systemctl status suricata` Active (running)
> - [ ] 🔴 Alert dengan group `suricata` tampil di Dashboard setelah trafik dipicu dari ATK


### Checklist Akhir DAY 2

> [!CAUTION]
> **Checklist Akhir DAY 2** — centang hanya jika:
>
> - [ ] 🔴 Serangan brute force berhasil disimulasikan dan dianalisis (2a-2c)
> - [ ] 🔴 Decoder custom dan sibling decoder berfungsi (2d-2e)
> - [ ] 🔴 Dynamic JSON decoding dipahami dan Suricata terintegrasi (2f)
> - [ ] 🔴 Snapshot **End-Day2** dibuat


</details>

<a id="tahap-3-day-3-cdb-lists-indexer-fim-inventory-rootkit"></a>
<details>
<summary><b>3️⃣ TAHAP 3: DAY 3 (CDB Lists, Indexer, FIM, Inventory, Rootkit)</b></summary>


Cakupan syllabus: CDB Lists, Ruleset Traversal, Indexer Advanced Pipeline, FIM, Inventory/Vulnerability, Rootkit Detection. Lab 3a sampai 3j.

### 3a. Modify Existing Rule [WZ]

> [!TIP]
> **Tujuan:** mengubah frequency/level rule bawaan tanpa menimpa file asli (pakai override di `local_rules.xml`).


1. Lihat rule SSHD default (contoh rule 5720, brute force, biasanya `frequency=8`, `timeframe=120`):

```bash
grep -A5 'id="5720"' /var/ossec/ruleset/rules/0095-sshd_rules.xml
```

2. Override jadi lebih sensitif (frequency 4 dalam 60 detik, level naik ke 12) di `local_rules.xml`:

```xml
<group name="local,syslog,sshd,">
  <rule id="100011" level="12" overwrite="no">
    <if_matched_sid>5716</if_matched_sid>
    <same_source_ip />
    <description>SSHD brute force (tuned): multiple failed logins</description>
    <frequency>4</frequency>
    <timeframe>60</timeframe>
    <group>authentication_failures,pci_dss_10.2.4,gdpr_IV_35.7.d,</group>
  </rule>
</group>
```

3. Restart dan uji ulang dengan hydra (3 percobaan cukup memicu):

```bash
systemctl restart wazuh-manager
```

```bash
# [ATK]
hydra -l labuser -P pass.txt ssh://192.168.56.125 -t 4 -f
```

**Checklist 3a**

> [!CAUTION]
> **Checklist 3a** — centang hanya jika:
>
> - [ ] 🔴 Rule 100011 muncul di alert dengan level 12 setelah percobaan brute force berikutnya
> - [ ] 🔴 Anda memahami kenapa override ditulis di `local_rules.xml`, bukan mengedit file ruleset bawaan langsung


### 3b. Basic Custom Rules [WZ]

```xml
<group name="myapp,">
  <rule id="100012" level="3">
    <decoded_as>myapp-fields</decoded_as>
    <description>MyApp: generic event</description>
  </rule>
  <rule id="100013" level="7">
    <if_sid>100012</if_sid>
    <field name="action">delete</field>
    <description>MyApp: delete action by $(user)</description>
  </rule>
</group>
```

Uji:

```bash
systemctl restart wazuh-manager
echo '2026-11-05 10:10:00 myapp: ERROR user=root action=delete result=denied' | /var/ossec/bin/wazuh-logtest
```

**Checklist 3b**

> [!CAUTION]
> **Checklist 3b** — centang hanya jika:
>
> - [ ] 🔴 Rule 100013 (child dari 100012) match dan level terbaca 7, bukan 3


### 3c. Advanced Custom Rule [WZ]

> [!TIP]
> **Tujuan:** rule lanjutan berbasis SSHD yang sudah ada (parent-child, 2 kondisi).


```xml
<group name="local,syslog,sshd,">
  <rule id="100014" level="13">
    <if_matched_sid>100011</if_matched_sid>
    <same_source_ip />
    <description>SSHD: repeated brute force from same source within short window, likely active attack</description>
    <frequency>2</frequency>
    <timeframe>300</timeframe>
    <mitre>
      <id>T1110</id>
    </mitre>
  </rule>
</group>
```

```bash
systemctl restart wazuh-manager
```

**Checklist 3c**

> [!CAUTION]
> **Checklist 3c** — centang hanya jika:
>
> - [ ] 🔴 Anda bisa menjelaskan rantai parent-child: 5716, 100011, 100014
> - [ ] 🔴 Rule memakai tag `<mitre>` yang muncul di Dashboard


### 3d. CDB Lists - Escalation [WZ]

> [!TIP]
> **Tujuan:** eskalasi alert berdasarkan daftar IP penyerang dikenal.


1. Buat CDB list:

```bash
vi /var/ossec/etc/lists/bad-ips
```

```
192.168.56.118:known-attacker
```

2. Build database CDB dan daftarkan di `ossec.conf`:

```bash
/var/ossec/bin/cdb_list /var/ossec/etc/lists/bad-ips
```

```xml
<ruleset>
  <list>etc/lists/bad-ips</list>
</ruleset>
```

3. Rule yang memakai list tersebut:

```xml
<group name="local,syslog,sshd,">
  <rule id="100015" level="15">
    <if_sid>5716</if_sid>
    <list field="srcip" lookup="address_match_key">etc/lists/bad-ips</list>
    <description>SSHD login attempt from known-bad IP $(srcip)</description>
  </rule>
</group>
```

```bash
systemctl restart wazuh-manager
```

**Checklist 3d**

> [!CAUTION]
> **Checklist 3d** — centang hanya jika:
>
> - [ ] 🔴 Serangan ulang dari ATK (192.168.56.118) memicu rule 100015 level 15
> - [ ] 🔴 Anda tahu lokasi dan format file CDB list (`key:value`, satu baris satu entri)


### 3e. SSH Watch Lists [WZ]

> [!TIP]
> **Tujuan:** rule yang melakukan lookup key dinamis di CDB dan mencocokkan berdasarkan **nilai** (bukan hanya keberadaan key).


```bash
vi /var/ossec/etc/lists/ssh-user-risk
```

```
labuser:high
admin:critical
```

```bash
/var/ossec/bin/cdb_list /var/ossec/etc/lists/ssh-user-risk
```

```xml
<group name="local,syslog,sshd,">
  <rule id="100016" level="10">
    <if_sid>5715</if_sid>
    <list field="srcuser" lookup="address_match_key_value" match="critical">etc/lists/ssh-user-risk</list>
    <description>SSHD: successful login as critical-risk user $(srcuser)</description>
  </rule>
</group>
```

```xml
<!-- tambahkan baris kedua di <ruleset><list>: -->
<list>etc/lists/ssh-user-risk</list>
```

```bash
systemctl restart wazuh-manager
```

```bash
# [UB] simulasikan login sukses sebagai admin (buat user admin dulu bila belum ada)
ssh admin@192.168.56.125
```

**Checklist 3e**

> [!CAUTION]
> **Checklist 3e** — centang hanya jika:
>
> - [ ] 🔴 Login SSH sukses sebagai user berisiko "critical" memicu rule 100016
> - [ ] 🔴 Anda memahami beda `address_match_key` (3d) vs `address_match_key_value` (3e)


### 3f. Index Lifecycle [WEB]

> [!TIP]
> **Tujuan:** kelola retensi data index Wazuh Indexer.


1. Dashboard, **Indexer management, Index Management, Index Policies**.
2. Buat policy baru: nama `wazuh-alerts-ism`, state **hot** (0 hari) -> **warm** (3 hari, opsional) -> **delete** (30 hari, untuk lab cukup 1 hari agar cepat diuji).
3. Terapkan ke index template `wazuh-alerts-*` (lihat Index Management, Templates).
4. Verifikasi lewat Dev Tools (Indexer):

```
GET _plugins/_ism/policies/wazuh-alerts-ism
```

**Checklist 3f**

> [!CAUTION]
> **Checklist 3f** — centang hanya jika:
>
> - [ ] 🔴 Policy ISM tersimpan dan terlihat di `GET _plugins/_ism/policies/wazuh-alerts-ism`
> - [ ] 🔴 Anda bisa menjelaskan tahap hot/warm/delete dan tujuannya (optimasi storage)


### 3g. Extending Pipeline [WZ] / [WEB]

> [!TIP]
> **Tujuan:** ingest pipeline kustom di Indexer (GeoIP, normalisasi field, routing, transformasi teks).


1. Dev Tools (Indexer), buat pipeline sederhana menambah field statis dan uppercase pada `data.srcip`-related tag:

```
PUT _ingest/pipeline/wazuh-custom-pipeline
{
  "processors": [
    { "set": { "field": "lab.source", "value": "essential-training" } },
    { "uppercase": { "field": "rule.description", "ignore_missing": true } }
  ]
}
```

2. Simulasikan ingest manual:

```
POST wazuh-alerts-4.x-test/_doc?pipeline=wazuh-custom-pipeline
{
  "rule": { "description": "test event" },
  "timestamp": "2026-11-05T10:00:00"
}
```

3. Cek hasil:

```
GET wazuh-alerts-4.x-test/_search
```

**Checklist 3g**

> [!CAUTION]
> **Checklist 3g** — centang hanya jika:
>
> - [ ] 🔴 Dokumen hasil `_search` menunjukkan `lab.source: essential-training` dan `rule.description` dalam huruf besar
> - [ ] 🔴 Anda memahami bahwa ini contoh konsep; pipeline produksi biasanya ditautkan ke index template `wazuh-alerts-*` lewat `default_pipeline`


### 3h. Customize Syscheck (File Integrity Monitoring) [WZ]

1. Tambahkan konfigurasi FIM ke group `linux-servers`:

```xml
<agent_config os="Linux">
  <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/myapp.log</location>
  </localfile>
  <localfile>
    <log_format>json</log_format>
    <location>/var/log/suricata/eve.json</location>
  </localfile>
  <syscheck>
    <directories check_all="yes" realtime="yes" whodata="yes" report_changes="yes">/etc</directories>
    <directories check_all="yes">/var/www</directories>
    <ignore>/etc/mtab</ignore>
    <ignore>/etc/hosts.deny</ignore>
    <frequency>43200</frequency>
  </syscheck>
</agent_config>
```

2. **[UB]** paksa restart agent agar konfigurasi tertarik, lalu ubah file untuk memicu alert:

```bash
sudo systemctl restart wazuh-agent
sudo sleep 20
sudo cp /etc/hostname /etc/hostname.bak
sudo bash -c 'echo "# test fim" >> /etc/hosts'
```

3. **[WZ]** verifikasi:

```bash
grep -i syscheck /var/ossec/logs/alerts/alerts.json | tail -n 5
```

**Checklist 3h**

> [!CAUTION]
> **Checklist 3h** — centang hanya jika:
>
> - [ ] 🔴 Alert FIM (group `syscheck`) muncul setelah perubahan `/etc/hosts`
> - [ ] 🔴 Alert menunjukkan **who-data** (user yang melakukan perubahan) dan **diff** isi file
> - [ ] 🔴 `/etc/mtab` tidak memicu alert (exclude berhasil)


### 3i. Inventory & Vulnerability Detection [WEB] / [WZ]

1. Dashboard, **Endpoints Summary / Inventory data** per agent: lihat **Installed packages**, **Processes**, **Network ports** (data syscollector).
2. Buka modul **Vulnerability Detection**, filter agent `orion`, lihat CVE yang terdeteksi dari paket terpasang.
3. Query via API:

```bash
curl -k -X GET 'https://localhost:55000/syscollector/001/packages?limit=5' -H "Authorization: Bearer $TOKEN"
curl -k -X GET 'https://localhost:55000/vulnerability/001?limit=5' -H "Authorization: Bearer $TOKEN"
```

**Checklist 3i**

> [!CAUTION]
> **Checklist 3i** — centang hanya jika:
>
> - [ ] 🔴 Dashboard menampilkan minimal satu paket software dan satu CVE untuk `orion`
> - [ ] 🔴 Query API `/vulnerability/001` mengembalikan data (bukan array kosong)


### 3j. Rootkit Detection [UB] / [WZ]

1. Pastikan rootcheck aktif (default pada `ossec.conf` agent, cek):

```bash
# [UB]
sudo grep -A3 '<rootcheck>' /var/ossec/etc/ossec.conf
```

2. Simulasikan indikasi mencurigakan: proses tersembunyi dari `ps` tapi terlihat di `/proc` (simulasi sederhana, bukan rootkit asli):

```bash
# [UB]
sleep 300 &
PID=$!
echo "PID simulasi: $PID"
ls /proc/$PID
```

3. Jalankan pemindaian manual dan lihat hasil:

```bash
# [UB]
sudo /var/ossec/bin/wazuh-control restart
```

```bash
# [WZ] tunggu siklus rootcheck berikutnya (default scan berkala), lalu cek:
grep -i rootcheck /var/ossec/logs/alerts/alerts.json | tail -n 5
```

**Checklist 3j**

> [!CAUTION]
> **Checklist 3j** — centang hanya jika:
>
> - [ ] 🔴 Anda memahami rootcheck memakai FIM untuk mendeteksi anomali file/proses/port/interface
> - [ ] 🔴 Anda sudah melihat minimal satu entri log terkait rootcheck (meski tidak ada rootkit sungguhan)


### Checklist Akhir DAY 3

> [!CAUTION]
> **Checklist Akhir DAY 3** — centang hanya jika:
>
> - [ ] 🔴 Rule tuning, custom rule, CDB list eskalasi berfungsi (3a-3e)
> - [ ] 🔴 Index lifecycle policy dan custom ingest pipeline teruji (3f-3g)
> - [ ] 🔴 FIM real-time dengan who-data aktif (3h)
> - [ ] 🔴 Data inventory dan vulnerability terlihat di Dashboard (3i)
> - [ ] 🔴 Rootcheck dipahami dan diverifikasi (3j)
> - [ ] 🔴 Snapshot **End-Day3** dibuat


</details>

<a id="tahap-4-day-4-active-response-integrasi-sca-cloud-sysmon-docker"></a>
<details>
<summary><b>4️⃣ TAHAP 4: DAY 4 (Active Response, Integrasi, SCA, Cloud, Sysmon, Docker)</b></summary>


Cakupan syllabus: Active Response, Integration System, Security Configuration Assessment, Cloud Security, Sysmon, Docker. Lab 4a sampai 4e.

### 4a. Active Response [WZ] / [UB]

> [!TIP]
> **Tujuan:** blokir otomatis IP penyerang begitu rule brute force (level tinggi) terpicu, lewat `iptables` (firewall-drop, stateful).


1. **[UB]** pastikan `iptables` ada (sudah diinstal di 0.2):

```bash
sudo which iptables
```

2. **[WZ]** daftarkan command dan active-response di `ossec.conf`:

```xml
<command>
  <name>firewall-drop</name>
  <executable>firewall-drop</executable>
  <timeout_allowed>yes</timeout_allowed>
</command>

<active-response>
  <disabled>no</disabled>
  <command>firewall-drop</command>
  <location>local</location>
  <rules_id>100011,100015</rules_id>
  <timeout>600</timeout>
</active-response>
```

```bash
systemctl restart wazuh-manager
```

3. Pastikan agent Ubuntu punya modul active response aktif (default sudah ada `wodle` di `ossec.conf` agent; biasanya tidak perlu diubah, cukup restart):

```bash
# [UB]
sudo systemctl restart wazuh-agent
```

4. **[ATK]** picu ulang brute force:

```bash
hydra -l labuser -P pass.txt ssh://192.168.56.125 -t 4 -f
```

5. **[UB]** verifikasi IP attacker sudah diblokir dan log active-response tercatat:

```bash
sudo iptables -L -n | grep 192.168.56.118
sudo grep -i active-response /var/ossec/logs/ossec.log | tail -n 5
```

6. **[ATK]** buktikan koneksi SSH ditolak:

```bash
ssh labuser@192.168.56.125   # harus timeout/refused
```

7. Setelah `timeout` 600 detik, verifikasi rule otomatis dihapus (stateful behaviour):

```bash
# [UB], tunggu > 10 menit lalu
sudo iptables -L -n | grep 192.168.56.118   # harus kosong
```

**Checklist 4a**

> [!CAUTION]
> **Checklist 4a** — centang hanya jika:
>
> - [ ] 🔴 IP 192.168.56.118 muncul di `iptables -L` setelah brute force terdeteksi
> - [ ] 🔴 Koneksi SSH dari ATK ditolak selama periode blok
> - [ ] 🔴 Setelah timeout, blokir otomatis dicabut (perilaku **stateful**)
> - [ ] 🔴 Anda bisa menjelaskan beda command **stateful** (ada undo otomatis, seperti firewall-drop) vs **stateless** (sekali jalan, tanpa undo)


### 4b. Slack Integration (Wazuh Integration System) [WZ]

> [!TIP]
> **Tujuan:** manager menjalankan skrip lokal (bawaan atau custom) saat alert level tinggi muncul, mengirim data alert plus elemen statis (webhook URL) ke layanan eksternal.


1. Buat **Incoming Webhook** Slack (di workspace test Anda) dan salin URL-nya.
2. Tambahkan integrasi di `ossec.conf`:

```xml
<integration>
  <name>slack</name>
  <hook_url>https://hooks.slack.com/services/XXXX/YYYY/ZZZZ</hook_url>
  <alert_format>json</alert_format>
  <level>10</level>
</integration>
```

```bash
systemctl restart wazuh-manager
```

3. Picu alert level >= 10 (ulangi hydra atau rule 100015 dari 3d) dan cek log integrasi:

```bash
tail -f /var/ossec/logs/integrations.log
```

4. (Opsional) buat skrip integrasi custom dari template bawaan sebagai referensi:

```bash
ls /var/ossec/integrations/   # lihat custom-virustotal, slack.py, pagerduty.py sebagai template
```

**Checklist 4b**

> [!CAUTION]
> **Checklist 4b** — centang hanya jika:
>
> - [ ] 🔴 Pesan alert muncul di channel Slack saat alert level >= 10 terjadi
> - [ ] 🔴 `integrations.log` tidak menunjukkan error terkait webhook
> - [ ] 🔴 Anda sudah melihat isi `slack.py` sebagai contoh template untuk integrasi custom


### 4c. Custom SCA (Security Configuration Assessment) [WZ] / [UB]

> [!TIP]
> **Tujuan:** audit kepatuhan konfigurasi mandiri dengan policy SCA, dan membuat check custom.


1. Cek policy SCA bawaan aktif di agent **[UB]**:

```bash
sudo grep -A10 '<sca>' /var/ossec/etc/ossec.conf
ls /var/ossec/ruleset/sca/   # (di manager) atau /var/ossec/etc/shared pada agent jika sudah sinkron
```

2. Buat policy SCA custom sederhana (cek permission file sensitif) di manager, lalu dorong ke group `linux-servers` **[WZ]**:

```bash
vi /var/ossec/ruleset/sca/sca_custom_lab.yml
```

```yaml
policy:
  id: "custom_lab_policy"
  file: "sca_custom_lab.yml"
  name: "Custom Lab Hardening Policy"
  description: "Basic checks created for Wazuh Essential Training lab"

checks:
  - id: 1
    title: "Ensure SSH root login is disabled"
    description: "PermitRootLogin should be no"
    rationale: "Prevent direct root SSH access"
    remediation: "Set PermitRootLogin no in /etc/ssh/sshd_config"
    compliance:
      - cis: ["5.2.8"]
    condition: all
    rules:
      - 'f:/etc/ssh/sshd_config -> r:^PermitRootLogin\s+no'
```

3. Tambahkan referensi policy ke `agent.conf` group `linux-servers` **[WZ]**:

```xml
<sca>
  <enabled>yes</enabled>
  <scan_on_start>yes</scan_on_start>
  <interval>12h</interval>
  <policies>
    <policy>sca_custom_lab.yml</policy>
  </policies>
</sca>
```

```bash
systemctl restart wazuh-manager
```

```bash
# [UB]
sudo systemctl restart wazuh-agent
```

4. Verifikasi di Dashboard: **Security Configuration Assessment**, agent `orion`, policy "Custom Lab Hardening Policy" tampil dengan hasil **passed/failed**.

**Checklist 4c**

> [!CAUTION]
> **Checklist 4c** — centang hanya jika:
>
> - [ ] 🔴 Policy custom tampil di Dashboard SCA untuk `orion`
> - [ ] 🔴 Check "Ensure SSH root login is disabled" berstatus sesuai konfigurasi aktual (passed jika `PermitRootLogin no` sudah diset)
> - [ ] 🔴 Anda bisa menyebut 1 kelebihan SCA dibanding CIS-CAT (native terintegrasi dengan Wazuh, agent-based, ringan) dan 1 kekurangannya (coverage/kematangan policy lebih terbatas dibanding CIS-CAT resmi)


### Cloud Security (overview, tidak wajib hands-on) [baca]

Tidak ada lab wajib di syllabus untuk modul ini; cukup pahami konsepnya:

- Wazuh memonitor **AWS** (CloudTrail, GuardDuty, Config), **Azure** (Activity Logs, Graph), **GCP** (Pub/Sub logs), **GitHub** (audit log), dan **Office 365** (Management Activity API).
- Cakupannya: instance/VM, layanan platform, audit log, dan **posture management** (misalnya cek compliance konfigurasi cloud).

**Checklist Cloud Security**

> [!CAUTION]
> **Checklist Cloud Security** — centang hanya jika:
>
> - [ ] 🔴 Anda bisa menyebutkan minimal 3 platform cloud yang didukung modul ini dan jenis data yang dipantau


### 4d. Sysmon Integration [WIN] / [WZ]

1. **[WIN]** unduh Sysmon dan konfigurasi dasar (SwiftOnSecurity config direkomendasikan komunitas; untuk lab, konfigurasi minimal berikut cukup):

```powershell
Invoke-WebRequest -Uri https://download.sysinternals.com/files/Sysmon.zip -OutFile $env:tmp\Sysmon.zip
Expand-Archive $env:tmp\Sysmon.zip -DestinationPath $env:tmp\Sysmon
cd $env:tmp\Sysmon
.\Sysmon64.exe -accepteula -i
Get-Service Sysmon64
```

2. **[WZ]** tambahkan Sysmon channel ke `agent.conf` group `windows-workstations`:

```xml
<agent_config os="Windows">
  <localfile>
    <location>Microsoft-Windows-Sysmon/Operational</location>
    <log_format>eventchannel</log_format>
  </localfile>
</agent_config>
```

```bash
systemctl restart wazuh-manager
```

3. **[WIN]** restart agent dan picu pola perintah mencurigakan (simulasi; perintah dijalankan di lab terisolasi, tidak benar-benar berbahaya):

```powershell
Restart-Service WazuhSvc
Start-Sleep -Seconds 15
powershell.exe -nop -w hidden -enc JABhACAAPQAgACcAdABlAHMAdAAnAA==
```

(Perintah di atas hanya mendekode string base64 sederhana `'test'`, memicu pola "PowerShell encoded command" yang umum dideteksi, tanpa payload berbahaya.)

4. **[WEB]** cek Dashboard: filter `agent.name: Fahmi` dan `rule.groups: sysmon`.

**Checklist 4d**

> [!CAUTION]
> **Checklist 4d** — centang hanya jika:
>
> - [ ] 🔴 Service `Sysmon64` Running
> - [ ] 🔴 Event Sysmon (Event ID 1, process creation) tampil di Dashboard untuk `Fahmi`
> - [ ] 🔴 Alert terkait pola "encoded command" PowerShell terdeteksi (jika ruleset Sysmon default mencakupnya; jika tidak muncul otomatis, catat bahwa event mentahnya tetap masuk untuk threat hunting manual)


### 4e. Docker Integration [UB] / [WZ]

1. **[UB]** instal Docker:

```bash
sudo apt -y install docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker orion
```

2. **[WZ]** aktifkan modul `docker-listener` di group `linux-servers`:

```xml
<agent_config os="Linux">
  <wodle name="docker-listener">
    <disabled>no</disabled>
    <run_on_start>yes</run_on_start>
  </wodle>
</agent_config>
```

```bash
systemctl restart wazuh-manager
```

3. **[UB]** restart agent lalu picu aksi container:

```bash
sudo systemctl restart wazuh-agent
sudo docker run -d --name lab-nginx nginx
sudo docker exec lab-nginx whoami
sudo docker stop lab-nginx
sudo docker start lab-nginx
sudo docker rm -f lab-nginx
```

4. **[WEB]** verifikasi Dashboard: filter `agent.name: orion` dan `rule.groups: docker`, pastikan event create/start/stop/exec/remove container tampil sebagai alert.

**Checklist 4e**

> [!CAUTION]
> **Checklist 4e** — centang hanya jika:
>
> - [ ] 🔴 Minimal 3 jenis event Docker (create, exec, remove) muncul sebagai alert di Dashboard


---


</details>

<a id="ringkasan-checklist-laboratory-exercises-sesuai-course-syllabus"></a>
<details>
<summary><b>✅ Ringkasan Checklist Laboratory Exercises (sesuai Course Syllabus)</b></summary>


Gunakan tabel ini sebagai rekap akhir; centang hanya setelah checklist detail pada bagian lab terkait (di atas) sudah lengkap.

### Session 1 - Setup & Agents

> [!CAUTION]
> **Session 1 - Setup & Agents** — centang hanya jika:
>
> - [ ] 🔴 [1a] Prepare Wazuh Server Components
> - [ ] 🔴 [1b] Navigate the Dashboard
> - [ ] 🔴 [1c] Register linux-agent
> - [ ] 🔴 [1d] Install Windows Agent
> - [ ] 🔴 [1e] Register via Dashboard
> - [ ] 🔴 [1f] Agent Upgrades
> - [ ] 🔴 [1g] Centralized Configuration


### Session 2 - Detection & Rules

> [!CAUTION]
> **Session 2 - Detection & Rules** — centang hanya jika:
>
> - [ ] 🔴 [2a] Brute Force Attack
> - [ ] 🔴 [2b] Assess Several Malicious Alerts
> - [ ] 🔴 [2c] Looking Up Rules
> - [ ] 🔴 [2d] Write a Custom Decoder
> - [ ] 🔴 [2e] Write a Sibling Decoder
> - [ ] 🔴 [2f] Wazuh's Dynamic JSON Decoder


### Session 3 - Monitoring & Inventory

> [!CAUTION]
> **Session 3 - Monitoring & Inventory** — centang hanya jika:
>
> - [ ] 🔴 [3a] Modify Existing Rule
> - [ ] 🔴 [3b] Basic Custom Rules
> - [ ] 🔴 [3c] Advanced Custom Rule
> - [ ] 🔴 [3d] CDB Lists - Escalation
> - [ ] 🔴 [3e] SSH Watch Lists
> - [ ] 🔴 [3f] Index Lifecycle
> - [ ] 🔴 [3g] Extending Pipeline
> - [ ] 🔴 [3h] Customize Syscheck
> - [ ] 🔴 [3i] Inventory & Vulnerability
> - [ ] 🔴 [3j] Rootkit Detection


### Session 4 - Response & Integrations

> [!CAUTION]
> **Session 4 - Response & Integrations** — centang hanya jika:
>
> - [ ] 🔴 [4a] Active Response
> - [ ] 🔴 [4b] Slack Integration
> - [ ] 🔴 [4c] Custom SCA
> - [ ] 🔴 [4d] Sysmon Integration
> - [ ] 🔴 [4e] Docker Integration


---


</details>

<a id="catatan-penutup"></a>
<details>
<summary><b>📝 Catatan Penutup</b></summary>


- Semua simulasi serangan (hydra, Sysmon encoded command, Docker exec) dalam tutorial ini ditujukan **murni untuk lab pribadi yang terisolasi**, sejalan dengan prasyarat 3.4-3.5 pada dokumen Recommended Prerequisites (memahami vulnerability/severity/attack pattern dan bekerja metodis dengan validasi di tiap langkah).
- Setelah 4 hari selesai, ulangi **Readiness Self-Assessment** (bagian 5 dokumen prerequisite) sebagai refleksi: berapa banyak keterampilan dari 20 poin tersebut yang kini sudah dikuasai lewat praktik di tutorial ini.
- Simpan snapshot `End-Day1` sampai `End-Day4` agar lab bisa diulang dari titik mana pun (konsep rollback, sesuai bagian 3.5 prerequisite).

</details>


---

<div align="center">

### 🛰️ PROJECT ORION — Built for hands-on SOC L1 practice

*"Centang hanya setelah bukti, bukan karena perintah sudah dijalankan."*

![Made with](https://img.shields.io/badge/Made%20with-Wazuh%20%2B%20Coffee-005571?style=flat-square&logo=wazuh&logoColor=white)

</div>
