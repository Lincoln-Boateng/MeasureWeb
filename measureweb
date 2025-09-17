#!/usr/bin/env python3
#-- coding: utf-8 --

"""
Measurement website 
Features:
- Area calculators for: square, rectangle, circle, triangle, trapezoid
- Unit conversions (length, area, volume, mass, temperature)

 Run: python measureweb.py
Then open http://127.0.0.1:5000/
"""

from flask import Flask, request, jsonify, render_template_string
import math

app = Flask(__name__)

# Frontend 

INDEX_HTML = """
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>MeasureWeb</title>
<style>
body { font-family: system-ui, Segoe UI, Roboto, Arial; margin: 0; padding: 0; background: #121212; color: #e0e0e0; }
header { background: #1f1f1f; padding: 16px; text-align: center; }
h1 { margin: 0; color: #00bcd4; }
.container { max-width: 800px; margin: 20px auto; padding: 20px; }
.card { background: #1e1e1e; padding: 16px; border-radius: 12px; margin-bottom: 20px; box-shadow: 0 4px 12px rgba(0,0,0,0.5); }
label { display: block; margin: 6px 0; transition: opacity 0.3s; }
input, select { padding: 8px; margin: 4px 0; border-radius: 6px; border: 1px solid #333; background: #2a2a2a; color: #e0e0e0; width: 100%; }
button { padding: 10px 16px; margin-top: 8px; cursor: pointer; border: none; border-radius: 6px; background: #00bcd4; color: #121212; font-weight: bold; transition: 0.2s; }
button:hover { background: #0097a7; }
h2 { margin-top: 0; color: #ffffff; }
.result { margin-top: 10px; font-weight: bold; color: #00e676; }
.modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); justify-content: center; align-items: center; }
.modal-content { background: #1e1e1e; padding: 20px; border-radius: 12px; max-width: 500px; color: #fff; }
.modal-content h3 { margin-top: 0; color: #00bcd4; }
.close { float: right; cursor: pointer; font-size: 18px; }
</style>
<script>

// Area Calculator

async function calculateArea() {
    const shape = document.getElementById('shape').value;
    const params = {};
    document.querySelectorAll('#area-form input').forEach(inp => {
        if (!inp.disabled && inp.value) params[inp.name] = inp.value;
    });
    const res = await fetch('/api/area', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ shape, params })
    });
    const data = await res.json();
    document.getElementById('area-result').innerText = data.result ? `Result: ${data.result}` : `Error: ${data.error}`;
}

function updateAreaInputs() {
    const shape = document.getElementById('shape').value;
    const fields = {
        side: ['square'],
        width: ['rectangle'],
        height: ['rectangle','triangle','trapezoid'],
        radius: ['circle'],
        base: ['triangle'],
        a: ['trapezoid'],
        b: ['trapezoid']
    };
    Object.keys(fields).forEach(id => {
        const input = document.querySelector(`input[name="${id}"]`);
        if (fields[id].includes(shape)) {
            input.parentElement.style.opacity = 1;
            input.disabled = false;
        } else {
            input.parentElement.style.opacity = 0.3;
            input.disabled = true;
        }
        input.value = "";
    });
}

function preventNegativeArea(evt) {
    if (evt.target.value < 0) evt.target.value = "";
}


// Unit Converter

async function convertUnit() {
    const category = document.getElementById('category').value;
    const value = document.getElementById('uvalue').value;
    const from = document.getElementById('ufrom').value;
    const to = document.getElementById('uto').value;
    const res = await fetch('/api/unit', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ category, value, from, to })
    });
    const data = await res.json();
    document.getElementById('unit-result').innerText = data.result ? `Result: ${data.result}` : `Error: ${data.error}`;
}

function preventNegativeUnit(evt) {
    const category = document.getElementById('category').value;
    if (evt.target.value < 0 && category !== 'temperature') {
        evt.target.value = "";
    }
}

async function loadUnits() {
    const res = await fetch('/api/units');
    const data = await res.json();
    const category = document.getElementById('category').value;
    const fromSelect = document.getElementById('ufrom');
    const toSelect = document.getElementById('uto');
    fromSelect.innerHTML = "";
    toSelect.innerHTML = "";
    data[category].forEach(unit => {
        const optionFrom = document.createElement('option');
        optionFrom.value = unit;
        optionFrom.textContent = unit;
        fromSelect.appendChild(optionFrom);
        const optionTo = document.createElement('option');
        optionTo.value = unit;
        optionTo.textContent = unit;
        toSelect.appendChild(optionTo);
    });
    fromSelect.selectedIndex = 0;
    toSelect.selectedIndex = 1;
}


// Info 

function toggleModal(show) {
    document.getElementById('info-modal').style.display = show ? 'flex' : 'none';
}


// Events

window.onload = () => {
    updateAreaInputs();
    document.getElementById('shape').addEventListener('change', updateAreaInputs);
    document.querySelectorAll('#area-form input').forEach(inp => inp.addEventListener('input', preventNegativeArea));

    loadUnits();
    document.getElementById('uvalue').addEventListener('input', preventNegativeUnit);
};
</script>
</head>
<body>
<header>
<h1>Measurement Toolkit</h1>
<button class="info-btn" onclick="toggleModal(true)">Info</button>
</header>
<div class="container">

<!-- Area Calculator -->
<div class="card">
<h2>Area Calculator</h2>
<form id="area-form" onsubmit="event.preventDefault(); calculateArea();">
<label>Shape:
<select id="shape">
<option value="square">Square</option>
<option value="rectangle">Rectangle</option>
<option value="circle">Circle</option>
<option value="triangle">Triangle</option>
<option value="trapezoid">Trapezoid</option>
</select>
</label>
<label>Side: <input type="number" step="any" name="side" placeholder="side" /></label>
<label>Width: <input type="number" step="any" name="width" /></label>
<label>Height: <input type="number" step="any" name="height" /></label>
<label>Radius: <input type="number" step="any" name="radius" /></label>
<label>Base: <input type="number" step="any" name="base" /></label>
<label>A: <input type="number" step="any" name="a" /></label>
<label>B: <input type="number" step="any" name="b" /></label>
<button type="submit">Calculate</button>
</form>
<div id="area-result" class="result"></div>
</div>

<!-- Unit Converter -->
<div class="card">
<h2>Unit Converter (Imperial ↔ Metric)</h2>
<form onsubmit="event.preventDefault(); convertUnit();">
<label>Category:
<select id="category" onchange="loadUnits()">
<option value="length">Length</option>
<option value="mass">Mass</option>
<option value="area">Area</option>
<option value="volume">Volume</option>
<option value="temperature">Temperature</option>
</select>
</label>
<label>Value: <input type="number" step="any" id="uvalue" /></label>
<label>From:
<select id="ufrom"></select>
</label>
<label>To:
<select id="uto"></select>
</label>
<button type="submit">Convert</button>
</form>
<div id="unit-result" class="result"></div>
</div>

</div>

<!-- Info Modal -->
<div id="info-modal" class="modal">
<div class="modal-content">
<span class="close" onclick="toggleModal(false)">&times;</span>
<h3>Supported Units</h3>
<p><strong>Length:</strong> m, cm, mm, km, in, ft, yd, mi</p>
<p><strong>Mass:</strong> kg, g, mg, lb, oz</p>
<p><strong>Area:</strong> m2, cm2, ft2, yd2, acre, hectare</p>
<p><strong>Volume:</strong> m3, l, ml, cm3, ft3, gal</p>
<p><strong>Temperature:</strong> C, F, K</p>
</div>
</div>
</body>
</html>
"""


# Area calculation

def area_square(side): return side * side
def area_rectangle(width, height): return width * height
def area_circle(radius): return math.pi * radius * radius
def area_triangle(base, height): return 0.5 * base * height
def area_trapezoid(a, b, height): return 0.5 * (a + b) * height

@app.route('/api/area', methods=['POST'])
def api_area():
    data = request.get_json() or {}
    shape = data.get('shape')
    params = data.get('params', {})
    try:
        if shape == 'square':
            return jsonify(result=area_square(float(params.get('side', 0))))
        if shape == 'rectangle':
            return jsonify(result=area_rectangle(float(params.get('width',0)), float(params.get('height',0))))
        if shape == 'circle':
            return jsonify(result=area_circle(float(params.get('radius',0))))
        if shape == 'triangle':
            return jsonify(result=area_triangle(float(params.get('base',0)), float(params.get('height',0))))
        if shape == 'trapezoid':
            return jsonify(result=area_trapezoid(float(params.get('a',0)), float(params.get('b',0)), float(params.get('height',0))))
        return jsonify(error='unknown shape'),400
    except Exception as e:
        return jsonify(error=str(e)),400


# Unit conversion

LENGTH_TO_M = {'m':1, 'cm':0.01, 'mm':0.001, 'km':1000, 'in':0.0254, 'ft':0.3048, 'yd':0.9144, 'mi':1609.344}
MASS_TO_KG = {'kg':1, 'g':0.001, 'mg':1e-6, 'lb':0.45359237, 'oz':0.028349523125}
AREA_TO_M2 = {'m2':1, 'cm2':0.0001, 'ft2':0.09290304, 'yd2':0.83612736, 'acre':4046.8564224, 'hectare':10000}
VOLUME_TO_M3 = {'m3':1, 'l':0.001, 'ml':1e-6, 'cm3':1e-6, 'ft3':0.028316846592, 'gal':0.003785411784}

@app.route('/api/unit', methods=['POST'])
def api_unit():
    data = request.get_json() or {}
    category = data.get('category')
    value = float(data.get('value',0))
    from_unit = data.get('from')
    to_unit = data.get('to')
    try:
        if category=='length':
            return jsonify(result=(value*LENGTH_TO_M[from_unit])/LENGTH_TO_M[to_unit])
        if category=='mass':
            return jsonify(result=(value*MASS_TO_KG[from_unit])/MASS_TO_KG[to_unit])
        if category=='area':
            return jsonify(result=(value*AREA_TO_M2[from_unit])/AREA_TO_M2[to_unit])
        if category=='volume':
            return jsonify(result=(value*VOLUME_TO_M3[from_unit])/VOLUME_TO_M3[to_unit])
        if category=='temperature':
            def to_celsius(val, unit):
                if unit=='C': return val
                if unit=='F': return (val-32)*5/9
                if unit=='K': return val-273.15
                raise ValueError('unknown temp unit')
            def from_celsius(val, unit):
                if unit=='C': return val
                if unit=='F': return val*9/5+32
                if unit=='K': return val+273.15
                raise ValueError('unknown temp unit')
            return jsonify(result=from_celsius(to_celsius(value, from_unit), to_unit))
        return jsonify(error='unknown category'),400
    except Exception as e:
        return jsonify(error=str(e)),400

@app.route('/api/units')
def api_units():
    return jsonify({
        "length": list(LENGTH_TO_M.keys()),
        "mass": list(MASS_TO_KG.keys()),
        "area": list(AREA_TO_M2.keys()),
        "volume": list(VOLUME_TO_M3.keys()),
        "temperature": ["C","F","K"]
    })

@app.route('/')
def index():
    return render_template_string(INDEX_HTML)

if __name__ == '__main__':
    app.run(debug=True)
