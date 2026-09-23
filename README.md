Radiantis.PART 1: CORE PYTHON SOURCE CODE (LATIN ALPHABET ENGINE v1.2)1.1 ENCODER MODULE (radiantis_encoder.py)

import colorsys
import os
import math
from PIL import Image, ImageDraw

class RadiantisEngine:
def init(self):
self.alphabet = "abcdefghijklmnopqrstuvwxyz"
self.color_map = {}
step = 360 / len(self.alphabet)
for index, letter in enumerate(self.alphabet):
self.color_map[letter] = index * step
self.color_map[' '] = None

def _char_to_rgb(self, char, index, total_chars):
char = char.lower()
if char not in self.color_map or self.color_map[char] is None:
return (240, 240, 240)

hue = self.color_map[char] / 360.0
saturation = 0.95
center_factor = 1.0 - (abs(index - (total_chars / 2)) / (total_chars / 2 + 1))
lightness = 0.45 + (0.15 * center_factor)

r, g, b = colorsys.hls_to_rgb(hue, lightness, saturation)
return (int(r * 255), int(g * 255), int(b * 255))

def generate_global_matrix(self, text, pixel_size=30, output_name="speclumen_matrix.png"):
clean_text = text.strip()
total_chars = len(clean_text)
if total_chars == 0: return

side = math.ceil(math.sqrt(total_chars))
img_side = side * pixel_size
image = Image.new("RGB", (img_side, img_side), (255, 255, 255))
draw = ImageDraw.Draw(image)

idx = 0
for row in range(side):
for col in range(side):
if idx < total_chars:
color = self._char_to_rgb(clean_text[idx], idx, total_chars)
else:
color = (255, 255, 255)

draw.rectangle([colpixel_size, rowpixel_size, (col+1)*pixel_size, (row+1)*pixel_size], fill=color)
idx += 1
image.save(output_name, "PNG")

1.2 DECODER MODULE (radiantis_decoder.py)

import colorsys
from PIL import Image

class RadiantisDecoder:
def init(self):
self.alphabet = "abcdefghijklmnopqrstuvwxyz"
self.color_map = {}
step = 360 / len(self.alphabet)
for index, letter in enumerate(self.alphabet):
self.color_map[index * step] = letter

def _find_closest_letter(self, hue_360):
if hue_360 is None: return ' '
closest_hue = min(self.color_map.keys(), key=lambda x: abs(x - hue_360))
if abs(closest_hue - hue_360) > 10.0: return ''
return self.color_map[closest_hue]

def decode_matrix_to_text(self, image_path, pixel_size=30):
try:
img = Image.open(image_path).convert("RGB")
except:
return ""
width, height = img.size
side = width // pixel_size
decoded_chars = []

for row in range(side):
for col in range(side):
x = col * pixel_size + (pixel_size // 2)
y = row * pixel_size + (pixel_size // 2)
r, g, b = img.getpixel((x, y))
if r > 235 and g > 235 and b > 235:
if len(decoded_chars) > 0 and decoded_chars[-1] != ' ':
decoded_chars.append(' ')
continue
r_norm, g_norm, b_norm = r / 255.0, g / 255.0, b / 255.0
h, l, s = colorsys.rgb_to_hls(r_norm, g_norm, b_norm)
letter = self._find_closest_letter(h * 360.0)
if letter: decoded_chars.append(letter)
return "".join(decoded_chars).strip()
