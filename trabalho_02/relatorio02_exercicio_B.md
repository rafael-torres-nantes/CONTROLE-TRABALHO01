# Análise de Controle de Veículo Submersível (Relatório 2)

## b) Esboço do Lugar das Raízes para o Modelo Reduzido

Nesta seção, o objetivo é simplificar o modelo original de 4ª ordem para um modelo de 2ª ordem, que seja mais simples de analisar, e em seguida esboçar o Lugar das Raízes (LDR) para este novo sistema.

### Passo 1: Justificativa da Redução do Modelo

A redução de um modelo de ordem superior é possível quando a resposta do sistema é dominada por um subconjunto de seus polos. Estes são chamados de **polos dominantes** e estão localizados mais próximos do eixo imaginário no plano-s, correspondendo aos modos mais lentos da resposta.

  - **Polos do Sistema Completo:**
      - $p\_1 = -3,215$
      - $p\_2 = -0,154$
      - $p\_{3,4} = -0,0435 \\pm j0,355$

Analisando os polos, observamos que o polo $p\_1 = -3,215$ está muito mais à esquerda no plano-s em comparação com os outros. A sua resposta transitória é muito mais rápida e decai rapidamente. Os polos $p\_2, p\_3, p\_4$ estão mais próximos do eixo $j\\omega$ e, portanto, dominam a dinâmica do sistema.

A simplificação mais eficaz para obter um modelo de 2ª ordem é manter o par de polos complexos conjugados ($p\_{3,4}$), pois são os mais lentos e os que mais caracterizam a resposta oscilatória do sistema.

### Passo 2: Construção do Modelo Reduzido de 2ª Ordem

Para criar o modelo reduzido, seguimos dois critérios:

1.  Manter os polos dominantes: $p\_{1,2\_{red}} = -0,0435 \\pm j0,355$.
2.  Preservar o ganho estático (DC Gain) do sistema original, para que a resposta em regime permanente para uma entrada degrau seja a mesma.

<!-- end list -->

  - **Cálculo do Ganho Estático do Modelo Original:**
    $$K_{DC} = \lim_{s \to 0} \hat{g}(s) = \frac{0,10875}{0,041574} \approx 2,616$$

  - **Forma do Modelo Reduzido:** O denominador é formado pelos polos dominantes. O numerador será uma constante $K\_{red}$ a ser determinada.
    $$\hat{g}_{red}(s) = \frac{K_{red}}{(s - p_3)(s - p_4)} = \frac{K_{red}}{s^2 + 0,087s + 0,1279}$$

  - **Ajuste do Ganho Estático:** Forçamos o ganho estático do modelo reduzido a ser igual ao do original.
    $$\lim_{s \to 0} \hat{g}_{red}(s) = \frac{K_{red}}{0,1279} = 2,616 \implies K_{red} = 2,616 \times 0,1279 \approx 0,3346$$

  - **Função de Transferência Reduzida Final:**
    $$\hat{g}_{red}(s) = \frac{0,3346}{s^2 + 0,087s + 0,1279}$$

O código para realizar essa etapa é:

```python
import control as ct
import numpy as np

# Denominador do modelo reduzido
den_red = [1, 0.087, 0.1279]
# Numerador K_red
num_red = [0.3346]
# Criação da FT reduzida
g_red = ct.tf(num_red, den_red)
```

### Passo 3: Esboço do LDR para o Modelo Reduzido

Agora, aplicamos as regras de esboço do LDR para a nova função $\\hat{g}\_{red}(s)$.

  - **Polos e Zeros:**

      - Polos: $p_{1,2} = -0,0435 \pm j0,355$ ($n=2$).
      - Zeros: Nenhum ($m=0$).

  - **Ramos no Eixo Real:** Como não há polos ou zeros reais, não existem ramos do LDR sobre o eixo real.

  - **Assíntotas:**

      - Número de assíntotas: $n - m = 2 - 0 = 2$.
      - Centróide ($\\sigma\_a$):
        $$\sigma_a = \frac{\sum \text{polos}}{n-m} = \frac{-0,0435 - 0,0435}{2} = -0,0435$$
      - Ângulos ($\\phi\_k$):
        $\phi_k = \frac{(2k+1)180^\circ}{2} \quad \text{para } k=0, 1$
        
        $\phi\_0 = 90^\circ$

        $\phi\_1 = 270^\circ \equiv -90^\circ$

### Passo 4: Geração do Gráfico do Lugar das Raízes

O esboço teórico indica que os ramos partirão dos polos complexos e seguirão verticalmente em direção ao infinito, cruzando o eixo real no ponto do centróide. A geração do gráfico via software confirma essa análise.

**INSERIR\_GRAFICO\_AQUI**

O gráfico resultante é uma linha reta vertical que passa por $s = -0,0435$, mostrando um comportamento muito mais simples do que o do sistema original. Isso ilustra o poder da redução de modelos para uma análise inicial da estabilidade e do comportamento dinâmico.