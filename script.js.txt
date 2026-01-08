const units = {
    length: { meters: 1, km: 1000, miles: 1609.34, feet: 0.3048 },
    mass: { kg: 1, grams: 0.001, pounds: 0.453592, ounces: 0.0283495 },
    temp: { celsius: 'c', fahrenheit: 'f', kelvin: 'k' }
};

const categorySelect = document.getElementById('category');
const fromUnit = document.getElementById('fromUnit');
const toUnit = document.getElementById('toUnit');
const inputValue = document.getElementById('inputValue');
const outputValue = document.getElementById('outputValue');
const saveBtn = document.getElementById('saveBtn');
const savedList = document.getElementById('savedList');

// Populate dropdowns based on category
function updateUnits() {
    const cat = categorySelect.value;
    const options = Object.keys(units[cat]);
    fromUnit.innerHTML = options.map(u => `<option value="${u}">${u}</option>`).join('');
    toUnit.innerHTML = options.map(u => `<option value="${u}">${u}</option>`).join('');
    convert();
}

function convert() {
    const val = parseFloat(inputValue.value);
    if (isNaN(val)) {
        outputValue.value = "";
        return;
    }

    const cat = categorySelect.value;
    const from = fromUnit.value;
    const to = toUnit.value;
    let result;

    if (cat === 'temp') {
        result = convertTemp(val, from, to);
    } else {
        // Convert to base unit (meters/kg) then to target unit
        const baseValue = val * units[cat][from];
        result = baseValue / units[cat][to];
    }

    outputValue.value = result.toFixed(4);
}

function convertTemp(val, from, to) {
    let celsius;
    if (from === 'celsius') celsius = val;
    else if (from === 'fahrenheit') celsius = (val - 32) * 5/9;
    else celsius = val - 273.15;

    if (to === 'celsius') return celsius;
    if (to === 'fahrenheit') return (celsius * 9/5) + 32;
    return celsius + 273.15;
}

saveBtn.addEventListener('click', () => {
    if (!outputValue.value) return;
    const log = `${inputValue.value} ${fromUnit.value} = ${outputValue.value} ${toUnit.value}`;
    const li = document.createElement('li');
    li.textContent = log;
    savedList.prepend(li);
});

categorySelect.addEventListener('change', updateUnits);
[inputValue, fromUnit, toUnit].forEach(el => el.addEventListener('input', convert));

// Initialize
updateUnits();