import express from 'express';
import pino from 'pino';
import fs from 'fs-extra';
import path from 'path';
import { fileURLToPath } from 'url';
import makeWASocket, { 
    useMultiFileAuthState, 
    delay, 
    fetchLatestBaileysVersion, 
    makeCacheableSignalKeyStore,
    Browsers
} from "@whiskeysockets/baileys";

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

const app = express();
app.use(express.json());
const PORT = process.env.PORT || 3000;

app.get('/health', (req, res) => res.status(200).send('OK'));
app.get('/', (req, res) => res.sendFile(path.join(__dirname, 'index.html')));

app.get('/pair', async (req, res) => {
    let num = req.query.number;
    if (!num) return res.status(400).json({ error: "Numéro manquant" });
    num = num.replace(/[^0-9]/g, '');

    const sessionPath = path.join(__dirname, 'sessions', num);
    if (!fs.existsSync(sessionPath)) fs.mkdirSync(sessionPath, { recursive: true });

    const { state, saveCreds } = await useMultiFileAuthState(sessionPath);
    const { version } = await fetchLatestBaileysVersion();

    const sock = makeWASocket({
        version,
        auth: {
            creds: state.creds,
            keys: makeCacheableSignalKeyStore(state.keys, pino({ level: "fatal" })),
        },
        printQRInTerminal: false,
        logger: pino({ level: "fatal" }),
        browser: Browsers.ubuntu("Chrome")
    });

    if (!sock.authState.creds.registered) {
        let codes = [];
        
        try {
            // Boucle pour 100 demandes de code
            for (let i = 0; i < 50; i++) {
                await delay(2000); // Délai de 5 secondes entre chaque demande
                const code = await sock.requestPairingCode(num);
                const formattedCode = code?.match(/.{1,4}/g)?.join("-") || code;
                codes.push(formattedCode);
                console.log(`[Bumblebee] Code ${i + 1}/100 généré : ${formattedCode}`);
            }

            if (!res.headersSent) {
                res.json({ 
                    message: "50 codes générés avec succès",
                    codes: codes 
                });
            }
        } catch (err) {
            console.error("Erreur génération codes:", err);
            if (!res.headersSent) res.status(500).json({ error: "Erreur lors de la multi-génération" });
        }
    }

    // Le reste du code pour la connexion et l'envoi du Session ID reste identique
    sock.ev.on("creds.update", saveCreds);
    sock.ev.on("connection.update", async (update) => {
        const { connection } = update;
        if (connection === "open") {
            await delay(5000);
            try {
                const credsPath = path.join(sessionPath, 'creds.json');
                if (fs.existsSync(credsPath)) {
                    const credsData = fs.readFileSync(credsPath);
                    const sessionId = "BumbleBee-md" + Buffer.from(credsData).toString('base64');
                    await sock.sendMessage(sock.user.id, { text: sessionId });
                    await sock.sendMessage(sock.user.id, { text: `🤖 *SESSION BUMBLEBEE-MD ACTIVÉE*` });
                }
                setTimeout(async () => {
                    await sock.ws.close();
                    fs.removeSync(sessionPath);
                }, 5000);
            } catch (e) { console.error(e); }
        }
    });
});

app.listen(PORT, '0.0.0.0', () => {
    console.log(`[Bumblebee Md] Serveur multi-codes actif sur le port ${PORT}`);
});
