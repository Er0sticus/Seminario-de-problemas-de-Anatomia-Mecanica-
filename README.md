# Operaciones Morfológicas en MATLAB

Este repositorio contiene código en MATLAB para aplicar operaciones morfológicas a imágenes.

## 📝 Descripción
El código procesa imágenes usando técnicas de procesamiento digital, como detección de bordes, dilatación y segmentación.

Aquí tienes un ejemplo de código:

```matlab
clc; clear; close all;

% Cargar imagen
I = imread('cell.tif'); 

% Convertir a RGB si es en escala de grises
if size(I,3) == 1
    I = cat(3, I, I, I);
end
```
# Cargar imagen 
imread('cell.tif') carga la imagen.
Si la imagen es en escala de grises, se convierte a RGB duplicando los canales.
 
## Covertir a escala de grises 
```matlab
I_gray = rgb2gray(I); % Convertir a escala de grises
BW = edge(I_gray, 'sobel'); % Detección de bordes
```

## Dilatar los bordes detectados 
```matlab
SE = strel('disk', 2); % Elemento estructurante circular
BW_dilated = imdilate(BW, SE); % Expande los bordes
```
strel('disk', 2) crea un elemento estructurante circular de radio 2.
imdilate(BW, SE) amplía los bordes, útil para unir regiones desconectadas.

## Rellenar huecos en los objetos 
```matlab
BW_filled = imfill(BW_dilated, 'holes'); % Rellenar huecos internos
```
imfill(BW_dilated, 'holes') cierra huecos internos en los objetos segmentados.
## Eliminar objetos tocadno los bordes
```matlab
BW_cleared = imclearborder(BW_filled); % Eliminar objetos en los bordes
```
imclearborder(BW_filled) elimina regiones pegadas a los bordes de la imagen.
## Suavizar la segmentación 
```matlab

BW_smoothed = imopen(BW_cleared, strel('disk', 3)); % Suaviza bordes
```
## Crear una superposición azul translúcidida
```matlab
alpha = 0.4; % Transparencia del efecto azul
overlay = im2double(I); % Convertir imagen a formato double

% Aplicar efecto azul
overlay(:,:,1) = overlay(:,:,1) .* (1 - BW_smoothed * alpha); % Reducir Rojo
overlay(:,:,2) = overlay(:,:,2) .* (1 - BW_smoothed * alpha); % Reducir Verde
overlay(:,:,3) = overlay(:,:,3) + (BW_smoothed * alpha); % Aumentar Azul

overlay = im2uint8(overlay); % Convertir de nuevo a uint8
```
## Mostrar todas las imágenes en una cuadrícula 
```matlab

figure;
set(gcf, 'Position', [100, 100, 1400, 700]); % Ajustar tamaño

sgtitle('Operaciones Morfológicas', 'FontSize', 14, 'FontWeight', 'bold');

subplot(2,4,1), imshow(I), title('Imagen Original');
subplot(2,4,2), imshow(I_gray), title('Escala de Grises');
subplot(2,4,3), imshow(BW), title('Binary Gradient Mask');
subplot(2,4,4), imshow(BW_dilated), title('Dilated Gradient Mask');
subplot(2,4,5), imshow(BW_filled), title('Filled Internal Holes');
subplot(2,4,6), imshow(BW_cleared), title('Objects Connected to Borders Removed');
subplot(2,4,7), imshow(BW_smoothed), title('Smoothed Object');
subplot(2,4,8), imshow(overlay), title('Final Segmentation (Soft Blue Overlay)');
```
sgtitle('Operaciones Morfológicas') → Agrega un título principal.
subplot(2,4,N) → Muestra las 8 etapas del proceso en una cuadrícula 2x4.
