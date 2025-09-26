# Análise de Controle de Veículo Submersível (Relatório 2)

## b) Esboço do Lugar das Raízes para o Modelo Reduzido

Nesta seção, o objetivo é simplificar o modelo original de 4ª ordem para um modelo de 2ª ordem, que seja mais simples de analisar, e em seguida esboçar o Lugar das Raízes (LDR) para este novo sistema.

### Passo 1: Justificativa da Redução do Modelo

A redução de um modelo de ordem superior é possível quando a resposta do sistema é dominada por um subconjunto de seus polos. Estes são chamados de **polos dominantes** e estão localizados mais próximos do eixo imaginário no plano-s, correspondendo aos modos mais lentos da resposta.

- **Polos do Sistema Completo:**
  - $p_1 = -3,215$
  - $p_2 = -0,154$
  - $p_{3,4} = -0,0435 \pm j0,355$

Analisando os polos, observamos que:
- O polo $p_1 = -3,215$ está muito mais à esquerda no plano-s em comparação com os outros. Sua resposta transitória é muito mais rápida e decai rapidamente (constante de tempo ≈ 0,31s).
- Os polos $p_3, p_4$ estão mais próximos do eixo $j\omega$ e, portanto, dominam a dinâmica do sistema (constante de tempo ≈ 23s).
- **O polo $p_2 = -0,154$ foi excluído** da redução porque, embora esteja mais próximo da origem que $p_1$, ele é:
  - **Um polo real** (não contribui para comportamento oscilatório)
  - **Menos dominante que os polos complexos** $p_{3,4}$, que possuem parte real menor em magnitude (-0,0435 vs -0,154)
  - Sua constante de tempo (≈ 6,5s) é significativamente menor que a dos polos complexos

A simplificação mais eficaz para obter um modelo de 2ª ordem é manter o **par de polos complexos conjugados ($p_{3,4}$)**, pois são os mais lentos e os que mais caracterizam a resposta oscilatória do sistema.

### Passo 2: Construção do Modelo Reduzido de 2ª Ordem

Para criar o modelo reduzido, seguimos dois critérios:

1. Manter os polos dominantes: $p_{1,2_{red}} = -0,0435 \pm j0,355$.
2. Preservar o ganho estático (DC Gain) do sistema original, para que a resposta em regime permanente para uma entrada degrau seja a mesma.

- **Cálculo do Ganho Estático do Modelo Original:**
  $$K_{DC} = \lim_{s \to 0} \hat{g}(s) = \frac{0,10875}{0,041574} \approx 2,616$$

- **Forma do Modelo Reduzido:** O denominador é formado pelos polos dominantes. O numerador será uma constante $K_{red}$ a ser determinada.
  $$\hat{g}_{red}(s) = \frac{K_{red}}{(s - p_3)(s - p_4)} = \frac{K_{red}}{s^2 + 0,087s + 0,1279}$$

- **Ajuste do Ganho Estático:** Forçamos o ganho estático do modelo reduzido a ser igual ao do original.
  $$\lim_{s \to 0} \hat{g}_{red}(s) = \frac{K_{red}}{0,1279} = 2,616 \implies K_{red} = 2,616 \times 0,1279 \approx 0,3346$$

- **Função de Transferência Reduzida Final:**
  $$\hat{g}_{red}(s) = \frac{0,3346}{s^2 + 0,087s + 0,1279}$$