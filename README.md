# NBN-Fahrzeug-JuGa-App
NBN Fahrzeug JuGa App
/fahrzeug-app
 ├── app.py
 ├── requirements.txt
 └── templates/
     └── index.html
     from flask import Flask, render_template, request

app = Flask(__name__)

anzahl_kinder = 0
gesamtplaetze = 0
fahrzeuge = []

@app.route("/", methods=["GET", "POST"])
def index():
    global anzahl_kinder, gesamtplaetze, fahrzeuge
    if request.method == "POST":
        if "anzahl_kinder" in request.form:
            # Anzahl der Kinder setzen
            anzahl_kinder = int(request.form["anzahl_kinder"])
            gesamtplaetze = 0
            fahrzeuge = []
        elif "plaetze" in request.form:
            # Fahrzeug hinzufügen
            plaetze = int(request.form["plaetze"])
            fahrzeuge.append(plaetze)
            gesamtplaetze += plaetze

    genug = gesamtplaetze >= anzahl_kinder and anzahl_kinder > 0
    return render_template("index.html",
                           anzahl_kinder=anzahl_kinder,
                           gesamtplaetze=gesamtplaetze,
                           fahrzeuge=fahrzeuge,
                           genug=genug)

if __name__ == "__main__":
    app.run(debug=True)

flask==3.0.0
gunicorn==21.2.0
<!DOCTYPE html>
<html>
<head>
    <title>Fahrzeug-Erfassung</title>
</head>
<body>
    <h1>Fahrzeug-Erfassung</h1>

    {% if anzahl_kinder == 0 %}
        <form method="post">
            <label>Anzahl der Kinder:</label>
            <input type="number" name="anzahl_kinder" required>
            <button type="submit">Start</button>
        </form>
    {% else %}
        <p>Benötigte Plätze: {{ anzahl_kinder }}</p>
        <p>Aktuell gemeldet: {{ gesamtplaetze }}</p>

        {% if not genug %}
            <form method="post">
                <label>Sitzplätze dieses Fahrzeugs:</label>
                <input type="number" name="plaetze" required>
                <button type="submit">Hinzufügen</button>
            </form>
        {% else %}
            <h2>Genug Plätze vorhanden! 🎉</h2>
            <p>Fahrzeuge: {{ fahrzeuge|length }}</p>
            <p>Gesamtplätze: {{ gesamtplaetze }}</p>
        {% endif %}
    {% endif %}
</body>
</html>
