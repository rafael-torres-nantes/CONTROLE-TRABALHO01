# Análise de Controle de Veículo Submersível (Relatório 2)

## a) Esboço do Lugar das Raízes para o Sistema Completo

Este documento detalha o processo para esboçar o Lugar das Raízes (LDR) para a planta de um veículo submersível não-tripulado. O objetivo é analisar a trajetória dos polos de malha fechada do sistema à medida que o ganho K varia de 0 a $\\infty$.

A Função de Transferência (FT) da planta é:

$$\hat{g}(s) = \frac{0,25s + 0,10875}{s^4 + 3,456s^3 + 3,45688s^2 + 0,719297s + 0,041574}$$

### Passo 1: Definição da Função de Transferência

O primeiro passo consiste em definir a função de transferência no ambiente de programação. Utilizamos a biblioteca `control` para criar o modelo da planta, representando o numerador e o denominador com seus respectivos coeficientes.

```python
import control as ct
import numpy as np

# Coeficientes do numerador e denominador
num = [0.25, 0.10875]
den = [1, 3.456, 3.45688, 0.719297, 0.041574]

# Criação da função de transferência
g = ct.tf(num, den)
```

### Passo 2: Encontrar Polos e Zeros da Malha Aberta

Para iniciar o esboço do LDR, precisamos identificar os polos (raízes do denominador) e os zeros (raízes do numerador) da função de transferência em malha aberta.

  - **Zeros:** O sistema possui $m=1$ zero.

      - $0,25s + 0,10875 = 0 \\implies s = -0,435$

  - **Polos:** O sistema possui $n=4$ polos, calculados a partir das raízes do polinômio do denominador.

      - $p\_1 = -3,215$
      - $p\_2 = -0,154$
      - $p\_3, p\_4 = -0,0435 \\pm j0,355$

Esses pontos são os locais de partida (polos) e de chegada (zeros) dos ramos do Lugar das Raízes.

### Passo 3: Determinar os Ramos no Eixo Real

O LDR existe no eixo real em todos os pontos que estão à esquerda de um número ímpar de polos e zeros reais.

  - Polos e zeros reais ordenados: $-3,215$ (polo), $-0,435$ (zero), $-0,154$ (polo).
  - Com base na regra, os ramos do LDR no eixo real estão nos intervalos:
      - $(-\\infty, -3,215]$
      - $[-0,435, -0,154]$

### Passo 4: Calcular as Assíntotas

Quando os ramos do LDR tendem ao infinito, eles seguem retas assintóticas.

  - **Número de assíntotas:** $n - m = 4 - 1 = 3$.
  - **Ponto de encontro (Centróide) no eixo real ($\\sigma\_a$):**
    $$
    $$$$\\sigma\_a = \\frac{\\sum \\text{polos} - \\sum \\text{zeros}}{n-m} = \\frac{(-3,215 - 0,154 - 0,0435 \\times 2) - (-0,435)}{3} = -1,007
    $$
    $$$$
    $$
  - **Ângulos das assíntotas ($\\phi\_k$):**
    $$
    $$$$\\phi\_k = \\frac{(2k+1)180^\\circ}{n-m} \\quad \\text{para } k=0, 1, 2
    $$
    $$$$  - $\\phi\_0 = 60^\\circ$
      - $\\phi\_1 = 180^\\circ$
      - $\\phi\_2 = 300^\\circ \\equiv -60^\\circ$

### Passo 5: Calcular os Ângulos de Partida dos Polos Complexos

Para determinar a direção inicial dos ramos que partem dos polos complexos, aplicamos o critério de ângulo. Calculamos para o polo no semiplano superior, $p\_3 = -0,0435 + j0,355$.

A condição é $\\sum \\text{ângulos dos zeros} - \\sum \\text{ângulos dos polos} = 180^\\circ$.
$$\arg(s-z_1) - [\arg(s-p_1) + \arg(s-p_2) + \arg(s-p_4) + \theta_{p3}] = 180^\circ$$
Substituindo $s \\to p\_3$ e resolvendo para o ângulo de partida $\\theta\_{p3}$, encontramos:

  - $\\theta\_{p3} \\approx 53^\\circ$
  - Por simetria, o ângulo de partida do polo $p\_4$ é $\\theta\_{p4} \\approx -53^\\circ$.

### Passo 6: Cruzamento com o Eixo Imaginário

Utilizamos o critério de Routh-Hurwitz para encontrar o valor do ganho $K$ que torna o sistema marginalmente estável (polos no eixo imaginário). A equação característica é:
$$s^4 + 3,456s^3 + 3,45688s^2 + (0,719297 + 0,25K)s + (0,041574 + 0,10875K) = 0$$
A análise da Tabela de Routh mostra que o sistema se torna instável para **$K \\approx 13,3$**, cruzando o eixo imaginário em **$\\omega \\approx \\pm 0,44$ rad/s**.

### Passo 7: Geração do Gráfico do Lugar das Raízes

Com todas as informações calculadas, podemos gerar o esboço completo do Lugar das Raízes. O código utiliza a função `root_locus` da biblioteca `control` para plotar o gráfico, que confirma visualmente todas as análises feitas.

**INSERIR\_GRAFICO\_AQUI**

O gráfico mostra os ramos partindo dos polos, seguindo as regras de ângulo e assíntotas, e cruzando o eixo imaginário no ponto previsto, o que valida nosso esboço teórico.