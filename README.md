# 🏛️ Active Directory Breaching & Initial Access Methodology

**Piattaforma:** TryHackMe  
**Stanza:** Intro to AD Breaching  
**Focus:** Active Directory, Network Penetration Testing, Initial Access, Credentials Harvesting  
**Tool Utilizzati:** Responder, Impacket (GetNPUsers), Kerbrute, CrackMapExec / Netexec  

---

## 📝 Executive Summary
Questo repository documenta le metodologie offensive utilizzate per ottenere la prima foothold (Initial Access) all'interno di un dominio Active Directory. L'analisi si focalizza sullo sfruttamento di configurazioni di rete insicure, debolezze nei protocolli di risoluzione nomi legacy e policy delle password vulnerabili.

---

## 🎯 Metodologia di Attacco (Initial Access Vectors)

### 1. LLMNR / NBT-NS Poisoning (Man-in-the-Middle)
Sfruttamento dei protocolli di fallback di Windows per la risoluzione dei nomi broadcast.
* **Meccanismo:** Quando un host non riesce a risolvere un nome tramite DNS, invia una richiesta LLMNR/NBT-NS. Tramite `Responder`, l'attaccante intercetta la richiesta fingendosi la risorsa cercata e cattura gli hash **NTLMv2** trasmessi dalla vittima.
* **Impatto:** Estrazione degli hash NTLMv2 per attacchi offline di password cracking (via Hashcat/John) o NTLM Relay.

### 2. Password Spraying
A differenza del Brute Force tradizionale (che rischia di bloccare gli account tramite le policy di lockout), il Password Spraying testa un'unica password comune (es. `Summer2026!`, `Welcome123!`) contro una lista numerosa di utenti validi.
* **Tool:** `Kerbrute` / `Netexec`.
* **Obiettivo:** Identificare account con credenziali deboli senza attivare gli alert di Account Lockout.

### 3. AS-REP Roasting
Tecnica offensiva diretta agli utenti di dominio che hanno il flag `DONT_REQ_PREAUTH` (Do not require Kerberos preauthentication) abilitato.
* **Esecuzione:** Invio di una richiesta AS-REQ per conto dell'utente target; il KDC risponde immediatamente con un messaggio AS-REP contenente una porzione cifrata con la chiave derivata dalla password dell'utente.
* **Tool:** `Impacket (GetNPUsers.py)`.
* **Obiettivo:** Estrazione del ticket cifrato da craccare offline per ottenere la password dell'account in chiaro.

---

## 🧠 Offensive Takeaways
1. **L'Anello Debole è la Configurazione:** Un ambiente AD non ha bisogno di exploit zero-day per essere violato; protocolli legacy abilitati (LLMNR) o singole configurazioni errate sugli utenti bastano a cedere le credenziali.
2. **Evasion & Lockout Avoidance:** Conoscere la differenza tra Brute Force e Password Spraying è ciò che distingue un attacco rumoroso e distruttivo da una penetrazione furtiva e professionale.
