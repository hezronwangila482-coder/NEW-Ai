from flask import Flask, request, jsonify, send_from_directory
import requests, os
app = Flask(__name__)

SYSTEM = """Wewe ni NEW Ai. Uliundwa na Hezron, student at MMUST (Masinde Muliro University of Science and Technology).
Unajua kila kitu: unaweza chat, kusaidia homework, coding, biashara, kuandika video scripts, ku-edit picha kwa maelezo, na ku-scan/elezea vitu.
Ukisalimiwa, jitambulishe: 'Mimi ni NEW Ai, niliundwa na Hezron, student wa MMUST'.
Jibu kwa Sheng / Kiswahili rahisi, uwe mcheshi, mkarimu na msaada sana. Uwe mfupi lakini umejaza maarifa."""

@app.route("/")
def home():
    return send_from_directory(".", "index.html")

@app.route("/chat", methods=["POST"])
def chat():
    msg = request.json.get("message", "")
    key = os.environ.get("GROQ_API_KEY", "")
    mode = request.json.get("mode", "chat") # chat, video, image, scan

    # Kama ni VIDEO - tengeneza script kamili
    if mode == "video":
        msg = f"Tengeneza video script kamili kwa hii idea: {msg}. Toa Title, Scene 1-5, maneno ya kusema (voiceover), na prompt ya AI video generator kama Runway/Pika. Iwe ya TikTok/YouTube."
    # Kama ni IMAGE - tengeneza prompt ya ku-edit
    elif mode == "image":
        msg = f"Nataka ku-edit / kutengeneza picha: {msg}. Niandikie prompt kamili ya DALL-E / Midjourney / Flux kwa Kiingereza, na nielezee nitafanya nini kuipata."
    # Kama ni SCAN - elezea kitu
    elif mode == "scan":
        msg = f"Mtu amescan / amepiga picha kitu na anasema: {msg}. Elezea ni nini, inafanya kazi aje, na kama ni homework / bidhaa / maandishi, msaidiwe kuielewa."

    try:
        r = requests.post("https://api.groq.com/openai/v1/chat/completions",
            headers={"Authorization": f"Bearer {key}"},
            json={"model": "llama-3.1-8b-instant",
                  "messages": [{"role": "system", "content": SYSTEM},
                               {"role": "user", "content": msg}]},
            timeout=40)
        d = r.json()
        return jsonify({"reply": d["choices"][0]["message"]["content"]})
    except Exception as e:
        return jsonify({"reply": f"Pole mkuu: {str(e)}"})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=int(os.environ.get("PORT", 5000)))
