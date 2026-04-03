# Settore 10 — Voice Mode & Device Nodes

> Guida completa alla voce e ai dispositivi satellite di OpenClaw: TTS/STT, Talk Mode, Voice Call Plugin (Twilio/Telnyx), Device Nodes (iOS/Android/macOS/headless), pairing, comandi esposti, e casi d'uso per Vendiamonoi.

---

## Indice

1. [Panoramica Voice & Nodes](#1-panoramica-voice--nodes)
2. [Voice Mode — TTS & STT](#2-voice-mode--tts--stt)
3. [Provider TTS](#3-provider-tts)
4. [Provider STT](#4-provider-stt)
5. [Talk Mode — Conversazione Continua](#5-talk-mode--conversazione-continua)
6. [Voice Call Plugin — Telefonate](#6-voice-call-plugin--telefonate)
7. [Device Nodes — Architettura](#7-device-nodes--architettura)
8. [Pairing — Connessione Dispositivi](#8-pairing--connessione-dispositivi)
9. [Comandi Esposti dai Nodi](#9-comandi-esposti-dai-nodi)
10. [Tipi di Nodo](#10-tipi-di-nodo)
11. [Sicurezza Voice & Nodes](#11-sicurezza-voice--nodes)
12. [Configurazione Vendiamonoi](#12-configurazione-vendiamonoi)
13. [Fonti & Riferimenti](#13-fonti--riferimenti)

---

## 1. Panoramica Voice & Nodes

OpenClaw estende le capacità dell'agente oltre il testo con due sistemi complementari:

| Sistema | Funzione | Metafora |
|---|---|---|
| **Voice Mode** | L'agente parla e ascolta | La voce dell'assistente |
| **Device Nodes** | L'agente accede a dispositivi fisici | Le mani, gli occhi, le orecchie |

### Come si complementano

```
Voice Mode →  L'agente parla con te (TTS) e ti ascolta (STT)
Device Nodes →  L'agente usa il tuo telefono (camera, notifiche, posizione)
Combinati →  "Ehi OpenClaw, fotografa questo documento" (voice + camera node)
```

---

## 2. Voice Mode — TTS & STT

### Come funziona

```
1. Utente parla (o invia voice note)
2. STT (Speech-to-Text) → trascrizione testo
3. AI processa il testo
4. AI genera risposta testuale
5. TTS (Text-to-Speech) → audio
6. Audio inviato all'utente
```

### Tre livelli di voice

| Livello | Descrizione | Complessità |
|---|---|---|
| **Voice notes** | Invia voice note, ricevi testo | Bassa — solo STT |
| **TTS reply** | Invia testo, ricevi audio | Media — solo TTS |
| **Talk Mode** | Conversazione continua bidirezionale | Alta — STT + TTS + loop |

---

## 3. Provider TTS

### Provider supportati

| Provider | Tipo | Qualità | Costo | Note |
|---|---|---|---|---|
| **ElevenLabs** | Cloud | Eccellente | $$$ (~$5-22/mese) | Voci più naturali, clonazione voce |
| **OpenAI TTS** | Cloud | Molto buona | $$ (~$15/1M chars) | 6 voci, bassa latenza |
| **Edge TTS** | Cloud (Microsoft) | Buona | $0 | Gratuito, usa Microsoft Edge neural TTS |
| **Kokoro** | Locale | Buona | $0 | Open source, zero API, privacy totale |
| **Piper** | Locale | Buona | $0 | Wyoming protocol, self-hosted |

### Confronto per Vendiamonoi

| Criterio | ElevenLabs | OpenAI | Edge TTS | Kokoro |
|---|---|---|---|---|
| Qualità italiano | Eccellente | Buona | Buona | Discreta |
| Latenza | ~200ms | ~300ms | ~150ms | ~100ms (locale) |
| Privacy | Cloud | Cloud | Cloud | Totale (locale) |
| Costo | $$$ | $$ | $0 | $0 |

### Configurazione TTS

```yaml
# ~/.openclaw/config.yaml
tts:
  provider: elevenlabs          # o openai, edge, kokoro
  elevenlabs:
    apiKey: "${ELEVENLABS_KEY}"
    voiceId: "21m00Tcm4TlvDq8ikWAM"   # Rachel (italiano)
    model: "eleven_turbo_v2_5"
  openai:
    apiKey: "${OPENAI_KEY}"
    voice: "nova"               # alloy, echo, fable, onyx, nova, shimmer
    model: "tts-1"              # tts-1 (veloce) o tts-1-hd (qualità)
```

---

## 4. Provider STT

### Provider supportati

| Provider | Tipo | Accuratezza | Costo | Note |
|---|---|---|---|---|
| **Whisper** (faster-whisper) | Locale | Eccellente | $0 | Default, dati restano sul tuo server |
| **OpenAI Whisper API** | Cloud | Eccellente | $0.006/min | Stesso modello, cloud |
| **Deepgram** | Cloud | Molto buona | $0.0043/min | Real-time, bassa latenza |

### Configurazione STT

```yaml
stt:
  provider: whisper             # Default, locale
  whisper:
    model: "medium"             # tiny, base, small, medium, large-v3
    language: "it"              # Forza italiano
    device: "cpu"               # o "cuda" per GPU
```

### Scelta modello Whisper

| Modello | VRAM | Velocità | Accuratezza | Consigliato per |
|---|---|---|---|---|
| tiny | ~1 GB | Velocissimo | Bassa | Test, bassa qualità OK |
| base | ~1 GB | Veloce | Discreta | Comandi semplici |
| small | ~2 GB | Medio | Buona | Uso quotidiano |
| medium | ~5 GB | Lento | Molto buona | Italiano, accenti |
| large-v3 | ~10 GB | Molto lento | Eccellente | Massima accuratezza |

---

## 5. Talk Mode — Conversazione Continua

### Come funziona

```
Loop continuo:
  1. Microfono ascolta (VAD — Voice Activity Detection)
  2. Utente parla → STT trascrive
  3. AI processa e genera risposta
  4. TTS sintetizza risposta
  5. Audio riprodotto
  6. Torna a 1.
```

### Implementazioni

| Implementazione | Descrizione | Latenza |
|---|---|---|
| **OpenClaw Voice** (Purple-Horizons) | App browser open-source, Whisper + ElevenLabs | ~1-2s |
| **Jupiter Voice** | Locale Whisper + Kokoro, zero cloud | ~0.5-1s |
| **OpenAI Realtime** | WebSocket diretto, STT+LLM+TTS in un hop | <500ms |
| **Talk Mode nativo** | App desktop separata che connette al Gateway | ~1-2s |

### OpenAI Realtime (più avanzato)

PR #25465 aggiunge un pipeline completo:

```
Twilio audio → OpenAI Realtime API (WebSocket) → STT + LLM + TTS
                                                    │
                                               Sub-second latency
                                                    │
                                                    ▼
                                              Audio response
```

Bypassa il Pi agent per latenza sotto il secondo. Ideale per telefonate.

---

## 6. Voice Call Plugin — Telefonate

### Overview

Il Voice Call Plugin permette a OpenClaw di fare e ricevere **telefonate reali** via provider VoIP.

### Provider supportati

| Provider | Tipo | Costo | Note |
|---|---|---|---|
| **Twilio** | Cloud VoIP | ~$0.013/min | Più popolare, affidabile |
| **Telnyx** | Cloud VoIP | ~$0.008/min | Alternativa più economica |
| **Plivo** | Cloud VoIP | ~$0.010/min | Terza opzione |
| **Mock** | Sviluppo | $0 | Per testing senza costi |

### Configurazione Twilio

```yaml
# Sezione plugins in config
plugins:
  entries:
    voice-call:
      config:
        provider: twilio
        fromNumber: "+39xxx"          # Numero Twilio acquistato
        twilio:
          accountSid: "ACxxx"
          authToken: "${TWILIO_TOKEN}"
        webhook:
          port: 3001
          path: "/voice/webhook"
          publicUrl: "https://openclaw.vendiamonoi.it"  # O ngrok/Tailscale
```

### Comandi CLI Voice Call

```bash
# Fare una telefonata
openclaw voicecall call --to "+393331234567" --message "Buongiorno, chiamiamo per l'ordine #1234"

# Continuare una conversazione in corso
openclaw voicecall continue --call-id <id> --message "Ha domande sull'ordine?"

# Parlare durante la chiamata
openclaw voicecall speak --call-id <id> --message "Un momento, verifico."

# Terminare la chiamata
openclaw voicecall hangup --call-id <id>
```

### Flusso chiamata

```
1. OpenClaw chiama via Twilio
2. Interlocutore risponde
3. Twilio stream audio → OpenAI Realtime (STT+LLM+TTS)
4. Risposta audio → Twilio → interlocutore
5. Loop conversazione fino a hangup
```

### Webhook

Il plugin ha bisogno di un URL pubblicamente raggiungibile per ricevere gli eventi da Twilio:
- **publicUrl**: URL diretto se hai un dominio
- **ngrok**: Tunnel per sviluppo locale
- **Tailscale Funnel**: Alternativa sicura senza esporre porte

---

## 7. Device Nodes — Architettura

### Cos'è un Node

Un **Node** è un dispositivo companion (telefono, tablet, PC, Raspberry Pi) che si connette al Gateway di OpenClaw via WebSocket e espone le sue capacità all'agente.

### Architettura

```
┌─────────────────┐
│  OpenClaw Gateway  │ ← Cervello (always-on)
│  (porta 18789)     │
└───────┬─────────┘
        │ WebSocket
  ┌─────┼───────────────┐
  │     │               │
  ▼     ▼               ▼
┌────┐ ┌───────┐ ┌───────────┐
│ iOS │ │ Android│ │ macOS       │
│ Node│ │ Node   │ │ Menubar App │
└────┘ └───────┘ └───────────┘
  │     │               │
  ▼     ▼               ▼
Camera  Camera       Canvas
Notifiche Posizione  Screenshot
Sensori  Clipboard   Comandi sistema
```

### Comunicazione

| Aspetto | Dettaglio |
|---|---|
| Protocollo | WebSocket con role: "node" |
| Autenticazione | Device pairing (identity + approval) |
| Invocazione | `node.invoke` RPC method |
| Persistenza | Connessione persistente, auto-reconnect |

---

## 8. Pairing — Connessione Dispositivi

### Come funziona il pairing

Ogni connessione deve presentare una **device identity** valida che il Gateway riconosce e ha approvato.

### Metodi di pairing

| Metodo | Come funziona | Quando usarlo |
|---|---|---|
| **QR Code** | Gateway genera QR con indirizzo + codice | Mobile (iOS/Android) |
| **Manuale** | Inserisci indirizzo e codice pairing | PC, server headless |
| **CLI** | `openclaw node run` da terminale | Headless, automazione |

### Step-by-step QR pairing

```
1. Sul server: QR code appare nel Dashboard o terminale
2. Sul telefono: Apri app OpenClaw → Scan QR
3. QR contiene: indirizzo server, porta, codice pairing
4. Gateway riceve richiesta pairing
5. Approva via CLI: openclaw devices approve <device-id>
6. Node connesso e operativo
```

### Gestione dispositivi

```bash
# Elencare dispositivi
openclaw devices list

# Approvare un dispositivo
openclaw devices approve <device-id>

# Revocare un dispositivo
openclaw devices revoke <device-id>

# Info dispositivo
openclaw devices info <device-id>
```

---

## 9. Comandi Esposti dai Nodi

### Command Surface

Ogni nodo espone una **command surface** — un set di comandi che l'agente può invocare.

### Comandi per piattaforma

| Comando | iOS | Android | macOS | Headless |
|---|---|---|---|---|
| `camera.capture` | ✅ | ✅ | ✅ | ❌ |
| `screen.snapshot` | ✅ | ✅ | ✅ | ❌ |
| `notifications.send` | ✅ | ✅ | ✅ | ❌ |
| `location.get` | ✅ | ✅ | ❌ | ❌ |
| `clipboard.read` | ✅ | ✅ | ✅ | ❌ |
| `clipboard.write` | ✅ | ✅ | ✅ | ❌ |
| `canvas.*` | ✅ | ✅ | ✅ | ❌ |
| `system.exec` | ❌ | ❌ | ✅ | ✅ |
| `file.read/write` | Limitato | Limitato | ✅ | ✅ |

### Canvas

Il sistema **Canvas** permette all'agente di mostrare contenuti sul display del nodo:
- Dashboard real-time (metriche, KPI)
- Notifiche visive
- Report e grafici
- Istruzioni per l'utente

```
# Esempio: mostrare KPI su tablet in ufficio
canvas.update({
  title: "Ordini Oggi",
  content: "47 ordini | 12 in attesa | 3 problemi"
})
```

---

## 10. Tipi di Nodo

### Mobile (iOS/Android)

| Aspetto | Dettaglio |
|---|---|
| App | OpenClaw app nativa |
| Pairing | QR Code |
| Capacità | Camera, notifiche, posizione, clipboard, canvas |
| Uso | Assistant mobile, notifiche push, foto documenti |

### Desktop (macOS)

| Aspetto | Dettaglio |
|---|---|
| App | Menubar app |
| Pairing | QR o manuale |
| Capacità | Canvas, screenshot, clipboard, system commands |
| Uso | Dashboard metriche, automazione desktop |

### Headless (Linux/Raspberry Pi)

| Aspetto | Dettaglio |
|---|---|
| App | `openclaw node run` (CLI) |
| Pairing | CLI + approve |
| Capacità | System exec, file read/write |
| Uso | Server remoti, build machines, IoT edge |

### Raspberry Pi

```bash
# Installare OpenClaw su Raspberry Pi
curl -fsSL https://install.openclaw.ai | bash

# Avviare come nodo
openclaw node run --gateway wss://openclaw.vendiamonoi.it:18789
```

Casi d'uso: monitoraggio magazzino, sensori temperatura, display KPI fisico.

---

## 11. Sicurezza Voice & Nodes

### Rischi Voice

| Rischio | Mitigazione |
|---|---|
| Audio intercettato | TLS/WSS per tutte le connessioni |
| Clonazione voce | Awareness, non usare voice auth |
| Comandi vocali non autorizzati | Conferma per azioni critiche |
| Costi TTS/chiamate fuori controllo | Budget cap, monitoring |

### Rischi Nodes

| Rischio | Mitigazione |
|---|---|
| Node non autorizzato | Device pairing obbligatorio |
| Accesso camera/microfono | Permessi espliciti, audit log |
| Comandi system.exec da nodo remoto | Sandbox, allowlist comandi |
| Nodo rubato/perso | Revoca immediata via CLI |

### Hardening checklist

```yaml
# Voice
1. Whisper locale per STT (dati non lasciano il server)
2. TTS via ElevenLabs/OpenAI solo se necessario
3. Edge TTS per zero costo senza dati sensibili
4. Budget cap per chiamate Twilio
5. Conferma utente per azioni critiche via voice

# Nodes
6. Device pairing obbligatorio per ogni nodo
7. Revoca immediata per dispositivi persi
8. Audit log di tutti i comandi node.invoke
9. Canvas: nessun dato sensibile su display condivisi
10. Headless nodes: allowlist comandi system.exec
```

---

## 12. Configurazione Vendiamonoi

### Voice — Setup raccomandato

```yaml
# Configurazione voice per Vendiamonoi
tts:
  provider: edge                 # Gratuito per notifiche audio
  # Oppure elevenlabs per qualità premium:
  # provider: elevenlabs
  # elevenlabs:
  #   apiKey: "${ELEVENLABS_KEY}"
  #   voiceId: "italiano-professionale"

stt:
  provider: whisper
  whisper:
    model: "small"               # Buon compromesso velocità/qualità
    language: "it"
    device: "cpu"
```

### Voice Call — Setup opzionale

```yaml
# Solo se serve: chiamate automatiche a fornitori
plugins:
  entries:
    voice-call:
      config:
        provider: twilio
        fromNumber: "+39xxx"
        twilio:
          accountSid: "${TWILIO_SID}"
          authToken: "${TWILIO_TOKEN}"
        webhook:
          publicUrl: "https://openclaw.vendiamonoi.it"
```

### Casi d'uso voice per Vendiamonoi

| Caso d'uso | Priorità | Provider | Costo |
|---|---|---|---|
| Notifiche audio su Telegram | Media | Edge TTS | $0 |
| Voice note da Alessio → comandi | Media | Whisper locale | $0 |
| Chiamate automatiche fornitori | Bassa | Twilio | ~$0.013/min |
| Morning briefing audio | Bassa | Edge/ElevenLabs | $0-5/mese |

### Nodes — Setup raccomandato

| Nodo | Dispositivo | Uso | Priorità |
|---|---|---|---|
| **iPhone Alessio** | iOS | Notifiche push, comandi rapidi, foto documenti | Alta |
| **Tablet ufficio** | iPad/Android | Dashboard KPI ordini real-time | Media |
| **Raspberry Pi magazzino** | RPi | Monitoraggio, sensori (opzionale) | Bassa |

### Costi stimati Voice & Nodes

| Componente | Costo/mese |
|---|---|
| STT (Whisper locale) | $0 |
| TTS (Edge TTS) | $0 |
| TTS (ElevenLabs premium) | $5-22 (opzionale) |
| Voice Call (Twilio) | $0-10 (a consumo) |
| Device Nodes | $0 |
| **Totale base** | **$0** |
| **Totale con premium** | **$5-32/mese** |

---

## 13. Fonti & Riferimenti

### Documentazione ufficiale
- TTS: https://docs.openclaw.ai/tools/tts
- Nodes: https://docs.openclaw.ai/nodes
- Voice Call Plugin: https://docs.openclaw.ai/plugins/voice-call
- Security: https://docs.openclaw.ai/gateway/security

### Voice
- LumaDock — TTS/STT/Talk Mode: https://lumadock.com/tutorials/openclaw-voice-tts-stt-talk-mode
- Meta Intelligence — Whisper + ElevenLabs: https://www.meta-intelligence.tech/en/insight-openclaw-voice
- OpenClaw Voice (browser-based): https://github.com/Purple-Horizons/openclaw-voice
- OpenAI Realtime PR: https://github.com/openclaw/openclaw/pull/25465
- Markaicode — Voice Messages: https://markaicode.com/openclaw-voice-messages-setup/

### Voice Call
- Voice Call npm: https://www.npmjs.com/package/@openclaw/voice-call
- Open-Claw.bot — Voice Call Setup: https://open-claw.bot/docs/tools/plugins/voice-call/
- DeepClaw (Deepgram): https://github.com/deepgram/deepclaw
- Telnyx — ClawdTalk: https://telnyx.com/resources/openclaw-phone-calls

### Device Nodes
- Nodes docs: https://docs.openclaw.ai/nodes
- DEV Community — Physical Devices: https://dev.to/hex_agent/openclaw-nodes-connecting-your-ai-agent-to-physical-devices-3253
- OpenClaw Nodes blog: https://www.openclawplaybook.ai/blog/openclaw-nodes-physical-devices/
- DeepWiki — Native Clients & Nodes: https://deepwiki.com/openclaw/openclaw/6-native-clients-and-nodes
- Raspberry Pi Setup: https://zooclaw.ai/help/en/2026-03-16/openclaw-raspberry-pi-setup/
- iOS/Android pairing: https://janaksenevirathne.medium.com/how-to-pair-ios-android-nodes-with-openclaw-gateway

---

> **Nota per Vendiamonoi**: Voice e Nodes sono funzionalità avanzate. Iniziare con: (1) Whisper locale per STT delle voice note di Alessio, (2) Edge TTS per notifiche audio su Telegram, (3) Nodo iPhone per notifiche push. Voice Call e tablet dashboard sono fase 2.

---

*Ultimo aggiornamento: aprile 2026*
*Documento parte della Master Class OpenClaw — vendiamonoi-knowledge*
