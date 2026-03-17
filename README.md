Pipeline de extração do dataset **UI-PRMD** (University of Idaho – Physical Rehabilitation Movement Data) para treinar modelos de detecção de anomalias de postura.
 
> Referência: Vakanski et al., *"A Data Set of Human Body Movements for Physical Rehabilitation Exercises"*, Data (Basel), 2018. [PMC5773117](https://pmc.ncbi.nlm.nih.gov/articles/PMC5773117/)

> Dataset: https://opendatalab.com/OpenDataLab/UI-PRMD
 
---
 
## Estrutura esperada dos arquivos de entrada
 
```
data/
├── correct/
│   ├── m01_s01_e01_angles.txt
│   ├── m01_s01_e02_angles.txt
│   └── ...
└── incorrect/
    ├── m01_s01_e01_angles_inc.txt
    ├── m01_s01_e02_angles_inc.txt
    └── ...
```
 
### Convenção de nomes
 
```
m{movimento}_s{sujeito}_e{execução}_angles.txt       → label 0 (correto)
m{movimento}_s{sujeito}_e{execução}_angles_inc.txt   → label 1 (incorreto)
```
 
O dataset contém **10 movimentos**, **10 sujeitos** e **10 execuções** por combinação, totalizando até 2000 arquivos (corretos + incorretos).
 
---
 
## Formato dos arquivos `.txt`
 
- Separador: vírgula
- Sem cabeçalho
- Cada linha = 1 frame de tempo (capturado a 100 Hz pelo sistema Vicon)
- Cada arquivo tem **168 frames** em média (varia por execução)
- **117 colunas** = 39 juntas × 3 eixos
 
| Eixo | Plano anatômico |
|------|----------------|
| X | Flexão / Extensão |
| Y | Abdução / Adução |
| Z | Rotação Interna / Externa |
 
---
 
## Ordem das 39 juntas (Vicon Angles — Tabela 2 do artigo)
 
| # | Nome | # | Nome |
|---|------|---|------|
| 1 | Head *(absoluto)* | 21 | Right_tibia |
| 2 | Left_head | 22 | Left_ankle |
| 3 | Right_head | 23 | Right_ankle |
| 4 | Left_neck | 24 | Left_foot |
| 5 | Right_neck | 25 | Right_foot |
| 6 | Left_clavicle | 26 | Left_toe |
| 7 | Right_clavicle | 27 | Right_toe |
| 8 | Thorax *(absoluto)* | 28 | Left_shoulder |
| 9 | Left_thorax | 29 | Right_shoulder |
| 10 | Right_thorax | 30 | Left_elbow |
| 11 | Pelvis *(absoluto)* | 31 | Right_elbow |
| 12 | Left_pelvis | 32 | Left_radius |
| 13 | Right_pelvis | 33 | Right_radius |
| 14 | Left_hip | 34 | Left_wrist |
| 15 | Right_hip | 35 | Right_wrist |
| 16 | Left_femur | 36 | Left_upperhand |
| 17 | Right_femur | 37 | Right_upperhand |
| 18 | Left_knee | 38 | Left_hand |
| 19 | Right_knee | 39 | Right_hand |
| 20 | Left_tibia | | |
 
> Juntas marcadas como *absoluto* têm coordenadas em relação ao sistema de referência do sensor. As demais são relativas à junta pai no modelo esquelético.
 
Para acessar a junta de índice `i` (base 0):
```python
col_X = i * 3
col_Y = i * 3 + 1
col_Z = i * 3 + 2
```
 
---
 
## Notebook disponível
 
### `data_extraction_csv.ipynb`
Lê todos os arquivos `.txt` e gera um CSV consolidado.
 
**Saída — `output/dataset.csv`:**
 
| Coluna | Descrição |
|--------|-----------|
| `movement` | Número do movimento (1–10) |
| `subject` | Número do sujeito (1–10) |
| `execution` | Número da execução (1–10) |
| `label` | 0 = correto, 1 = incorreto |
| `frame` | Índice do frame dentro do arquivo |
| `Head_X` … `Right_hand_Z` | 117 ângulos em graus (°) |
 
**Como rodar:**
1. Defina `CORRECT_DIR` e `INCORRECT_DIR` na célula de configuração
2. Execute todas as células em ordem
3. O CSV é escrito
 
---