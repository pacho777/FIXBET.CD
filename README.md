# FIXBET.CD
site de comparateur des parie sportifs  et bookmaker combiné des site des paries 
pip install flask
pari_comparatif/
│
├── app.py
├── templates/
│   ├── index.html
│   └── coupon.html
from flask import Flask, render_template, request

app = Flask(__name__)

# Données simulées des bookmakers (ex: RDC)
bookmakers = [
    {
        "match": "TP Mazembe vs Vita Club",
        "bookmaker": "1XBet",
        "cote": 1.85
    },
    {
        "match": "TP Mazembe vs Vita Club",
        "bookmaker": "Betika",
        "cote": 1.90
    },
    {
        "match": "AS Maniema vs DC Motema",
        "bookmaker": "SportyBet",
        "cote": 2.10
    },
    {
        "match": "Real Madrid vs Barca",
        "bookmaker": "1XBet",
        "cote": 2.30
    },
    {
        "match": "Chelsea vs Arsenal",
        "bookmaker": "Bet365",
        "cote": 1.95
    }
]

@app.route("/")
def index():
    return render_template("index.html", bookmakers=bookmakers)

@app.route("/coupon", methods=["POST"])
def coupon():
    selections = request.form.getlist("pari")
    
    if len(selections) != 5:
        return "⚠️ Veuillez sélectionner exactement 5 paris"

    coupon = []
    cote_totale = 1

    for s in selections:
        match, bookmaker, cote = s.split("|")
        cote = float(cote)
        cote_totale *= cote
        coupon.append({
            "match": match,
            "bookmaker": bookmaker,
            "cote": cote
        })

    return render_template(
        "coupon.html",
        coupon=coupon,
        cote_totale=round(cote_totale, 2)
    )

if __name__ == "__main__":
    app.run(debug=True)
<!DOCTYPE html>
<html>
<head>
    <title>Comparatif de Paris Sportifs RDC</title>
</head>
<body>
<h2>Comparatif de Paris Sportifs</h2>

<form method="post" action="/coupon">
<table border="1">
<tr>
    <th>Sélection</th>
    <th>Match</th>
    <th>Bookmaker</th>
    <th>Cote</th>
</tr>

{% for b in bookmakers %}
<tr>
    <td>
        <input type="checkbox" name="pari"
        value="{{ b.match }}|{{ b.bookmaker }}|{{ b.cote }}">
    </td>
    <td>{{ b.match }}</td>
    <td>{{ b.bookmaker }}</td>
    <td>{{ b.cote }}</td>
</tr>
{% endfor %}

</table>

<br>
<button type="submit">Créer un coupon (5 paris)</button>
</form>

</body>
</html>
<!DOCTYPE html>
<html>
<head>
    <title>Coupon de Pari</title>
</head>
<body>

<h2>🎟️ Coupon de Pari Sportif</h2>

<ul>
{% for c in coupon %}
    <li>{{ c.match }} | {{ c.bookmaker }} | Cote: {{ c.cote }}</li>
{% endfor %}
</ul>

<h3>📊 Cote totale : {{ cote_totale }}</h3>

</body>
</html>