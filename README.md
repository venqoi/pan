# Pan Flag

Pansexual flan using github languages list

![image](https://files.catbox.moe/eq2mlw.png)


Forked from https://github.com/Vendicated/gay


<details>
<summary>As I had trouble using Vendicated's script I made my own in python</summary>

```py
import yaml
from colormath.color_objects import LabColor, sRGBColor
from colormath.color_diff import delta_e_cie2000
import urllib.request

url = "https://raw.githubusercontent.com/github-linguist/linguist/master/lib/linguist/languages.yml"
response = urllib.request.urlopen(url)
data = yaml.safe_load(response)

target_colors = [
    {"name": "Magenta", "hex": "#FF218C"},
    {"name": "Yellow", "hex": "#FFD800"},
    {"name": "Cyan", "hex": "#21B1FF"}
]

target_color_objects = [LabColor(*sRGBColor.new_from_rgb_hex(color["hex"]).get_value_tuple()) for color in target_colors]

def delta_e_cie2000_manual(color1, color2):
    L1, a1, b1 = color1.lab_l, color1.lab_a, color1.lab_b
    L2, a2, b2 = color2.lab_l, color2.lab_a, color2.lab_b
    delta_L = L2 - L1
    delta_a = a2 - a1
    delta_b = b2 - b1
    delta_C = (delta_a ** 2 + delta_b ** 2) ** 0.5
    delta_H_sq = delta_a ** 2 + delta_b ** 2 - delta_C ** 2
    k_L = 1
    k_C = 1
    k_H = 1
    k_1 = 0.045
    k_2 = 0.015
    C_bar = (delta_C + (k_1 * delta_L) / (k_L * L1)) ** 2
    H_bar = delta_H_sq / ((1 + k_1 * (L1 ** 2)) * (1 + k_2 * (L1 ** 2))) 
    delta_E = (delta_L / (k_L * L1)) ** 2 + C_bar + H_bar
    return delta_E ** 0.5

for target_color in target_color_objects:
    closest_color = None
    closest_distance = float('inf')
    for language, values in data.items():
        if 'color' in values:
            color = LabColor(*sRGBColor.new_from_rgb_hex(values['color']).get_value_tuple())
            distance = delta_e_cie2000_manual(target_color, color)
            if distance < closest_distance:
                closest_color = values['color']
                closest_distance = distance
                closest_language = language

    print(f"For target color {target_color}, closest color in the YAML file is '{closest_color}' in the language '{closest_language}' with a delta E value of {closest_distance:.2f}.")
```

</details>
