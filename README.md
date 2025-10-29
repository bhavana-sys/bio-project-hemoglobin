# bio-project-hemoglobin
Visualization of hemoglobin quaternary structure using Python and py3Dmol
!pip install py3Dmol biopython matplotlib numpy pandas > /dev/null

import py3Dmol

import matplotlib.pyplot as plt

import numpy as np

import pandas as pd

from IPython.display import display, HTML

def show_complete_structure():
    view = py3Dmol.view(query='pdb:1HHO')
    view.setStyle({'cartoon': {'color': 'spectrum'}})
    view.zoomTo()
    return view.show()

def show_alpha_chains():
    view = py3Dmol.view(query='pdb:1HHO')
    view.setStyle({'chain':'A'}, {'cartoon': {'color':'salmon'}})
    view.setStyle({'chain':'C'}, {'cartoon': {'color':'red'}})
    view.zoomTo()
    return view.show()

def show_beta_chains():
    view = py3Dmol.view(query='pdb:1HHO')
    view.setStyle({'chain':'B'}, {'cartoon': {'color':'lightblue'}})
    view.setStyle({'chain':'D'}, {'cartoon': {'color':'blue'}})
    view.zoomTo()
    return view.show()

def show_heme_groups():
    view = py3Dmol.view(query='pdb:1HHO')
    view.setStyle({'cartoon': {'color':'grey', 'opacity':0.3}})
    view.addStyle({'resn':'HEM'}, {'stick':{'colorscheme':'greenCarbon'}})
    view.addStyle({'elem':'FE'}, {'sphere':{'color':'orange', 'radius':1.0}})
    view.zoomTo({'resn':'HEM'})
    return view.show()

def plot_oxygen_binding_curve():
    po2 = np.linspace(0, 100, 100)
    hill_coefficient = 2.8
    p50_hb = 26
    saturation_hb = 100 * (po2**hill_coefficient) / (p50_hb**hill_coefficient + po2**hill_coefficient)
    p50_mb = 5
    saturation_mb = 100 * po2 / (p50_mb + po2)

    plt.figure(figsize=(7,5))
    plt.plot(po2, saturation_hb, 'r-', label='Hemoglobin (cooperative)')
    plt.plot(po2, saturation_mb, 'b--', label='Myoglobin (non-cooperative)')
    plt.axvline(40, color='green', linestyle=':', label='Tissue pO₂')
    plt.axvline(100, color='purple', linestyle=':', label='Lung pO₂')
    plt.title('Oxygen Binding Curves', fontsize=14)
    plt.xlabel('pO₂ (mmHg)')
    plt.ylabel('% Saturation')
    plt.legend()
    plt.grid(True)
    plt.show()

def show_amino_acid_table():
    data = {
        "Property": [
            "Total number of amino acids",
            "Number of polar amino acids",
            "Number of non-polar amino acids",
            "Number of hydrogen bonds",
            "Number of S–S bonds (due to H bonding)"
        ],
        "Value": [
            574,
            "≈ 250",
            "≈ 324",
            "≈ 3200",
            0
        ]
    }

    df = pd.DataFrame(data)
    styled = (
        df.style.set_table_styles([
            {'selector': 'th', 'props': [('background-color', '#006666'), ('color', 'white'), ('font-weight', 'bold'), ('text-align', 'center')]},
            {'selector': 'td', 'props': [('text-align', 'center'), ('font-size', '15px'), ('background-color', '#E0F2F1')]}
        ])
        .hide(axis="index")
    )

    display(HTML("<div style='background-color:#004D4D; color:white; padding:8px; border-radius:8px; text-align:center;'><h3>🧫 Hemoglobin Amino Acid Composition</h3></div>"))
    display(styled)


def show_structure_info():
    html = """
    <h2>🧠 Hemoglobin Quaternary Structure</h2>
    <p>Hemoglobin is a protein found in red blood cells that transports oxygen throughout the body.</p>
    <ul>
    <li><b>Subunits:</b> Two alpha (α) and two beta (β) chains</li>
    <li><b>Heme groups:</b> Each subunit carries one heme group with an iron atom for oxygen binding</li>
    <li><b>Symmetry:</b> Approximate two-fold symmetry between αβ dimers</li>
    </ul>

    <h3>Subunit Interfaces</h3>
    <ul>
    <li><b>α1–β1, α2–β2:</b> Stable contacts</li>
    <li><b>α1–β2, α2–β1:</b> Flexible interfaces involved in conformational changes</li>
    </ul>

    <h2>🌬 Cooperative Oxygen Binding</h2>
    <p>Binding of oxygen to one subunit increases affinity in others — known as <b>positive cooperativity</b>.</p>
    <ul>
    <li><b>T-state (deoxy):</b> Low affinity, stabilized by salt bridges</li>
    <li><b>R-state (oxy):</b> High affinity, stabilized by oxygen binding</li>
    </ul>

    <h2>⚙ Allosteric Regulation</h2>
    <ul>
    <li><b>2,3-BPG:</b> Stabilizes the T-state (reduces oxygen affinity)</li>
    <li><b>CO₂ and H⁺ (Bohr effect):</b> Promote oxygen release in tissues</li>
    </ul>

    <h2>💉 Medical Relevance</h2>
    <ul>
    <li><b>Sickle Cell Anemia:</b> Mutation in β chain affects quaternary structure</li>
    <li><b>Thalassemia:</b> Reduced synthesis of α or β chains</li>
    <li><b>CO Poisoning:</b> CO binds tightly to heme, preventing oxygen transport</li>
    </ul>
    """
    display(HTML(html))

print("🧬 Complete Structure:")
show_complete_structure()
print("\n🧩 Alpha Chains:")
show_alpha_chains()
print("\n🔷 Beta Chains:")
show_beta_chains()
print("\n🩸 Heme Groups:")
show_heme_groups()
print("\n📈 Oxygen Binding Curve:")
plot_oxygen_binding_curve()
print("\n📊 Amino Acid Composition:")
show_amino_acid_table()
print("\n📘 Structure & Functional Information:")
show_structure_info()
