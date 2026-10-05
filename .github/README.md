const makeWASocket = require("@whiskeysockets/baileys").default;
const {
  useMultiFileAuthState,
  DisconnectReason
} = require("@whiskeysockets/baileys");

async function startBot() {
  const { state, saveCreds } =
    await useMultiFileAuthState("session");

  const sock = makeWASocket({
    auth: state
  });

  sock.ev.on("creds.update", saveCreds);

  sock.ev.on("messages.upsert", async ({ messages }) => {
    const msg = messages[0];

    if (!msg.message || msg.key.fromMe) return;

    const text =
      msg.message.conversation ||
      msg.message.extendedTextMessage?.text ||
      "";

    if (text === ".menu") {
      await sock.sendMessage(msg.key.remoteJid, {
        text: `🤖 *MENU BOT*

.menu
.ping
.info`
      });
    }

    if (text === ".ping") {
      await sock.sendMessage(msg.key.remoteJid, {
        text: "🏓 Pong!"
      });
    }
  });
}

startBot();
