# Análise de Controle de Veículo Submersível (Relatório 2)

## d) Projeto de Compensador para o Modelo Reduzido

Nesta seção, projetamos um compensador para o sistema de 2ª ordem (`g_red(s)`) com o objetivo de atender a um conjunto de requisitos de desempenho em malha fechada. A estratégia utilizada é a de alocação de polos dominantes.

### Passo 1: Definição dos Requisitos de Desempenho

Os requisitos de projeto são traduzidos em restrições matemáticas no plano-s, que definirão uma **Região de Desempenho Garantido (Ω)**.

1.  **Erro Estático Nulo:**
    -   **Lógica:** Para obter erro estático nulo para uma entrada degrau, o sistema em malha aberta deve ser do **Tipo 1**. Como a planta reduzida é do Tipo 0, o compensador precisa introduzir um **polo na origem (integrador)**.
    -   **Estrutura do Compensador:** A forma do compensador será a de um PI (Proporcional-Integral) ou avanço/atraso com um integrador: $C(s) = \frac{K_c(s+z_c)}{s}$.

2.  **Overshoot ($M_p$) inferior a 20%:**
    -   **Lógica:** O overshoot está diretamente relacionado ao fator de amortecimento ($\zeta$).
    -   **Cálculo:**
        $$M_p = e^{\frac{-\pi\zeta}{\sqrt{1-\zeta^2}}} < 0,2 \implies \zeta > 0,456$$
    -   **Restrição no plano-s:** Esta condição define uma região cônica com um ângulo $\beta = \arccos(\zeta) < 62,8^\circ$ em relação ao eixo real negativo.

3.  **Tempo de Acomodação ($t_s$) inferior a 10s (critério 5%):**
    -   **Lógica:** O tempo de acomodação está relacionado à parte real dos polos dominantes ($\sigma = \zeta\omega_n$).
    -   **Cálculo:**
        $$t_s = \frac{3}{\sigma} < 10 \text{s} \implies \sigma > 0,3$$
    -   **Restrição no plano-s:** Os polos dominantes devem estar à esquerda da linha vertical $Re(s) = -0,3$.

### Passo 2: Definição e Plotagem da Região Ω

As restrições de $\zeta$ e $\sigma$ definem a região Ω no semiplano esquerdo onde os polos dominantes de malha fechada devem ser alocados. Plotamos essa região para visualizar a área de projeto válida.

**INSERIR_GRAFICO_AQUI**

### Passo 3: Escolha dos Polos Dominantes Desejados ($s_d$)

Com base na região Ω, escolhemos um par de polos complexos conjugados ($s_d$) que servirão como alvo para o nosso projeto. A escolha foi feita com uma margem de segurança para garantir o cumprimento dos critérios.

-   **Polo Desejado:** $s_d = -0,4 + j0,532$.
-   Este ponto está confortavelmente dentro da região Ω, pois:
    -   $Re(s_d) = -0,4$ (mais rápido que o mínimo de $-0,3$).
    -   O fator de amortecimento correspondente é $\zeta = \cos(\arctan(\frac{0,532}{0,4})) \approx 0,6$, que é maior que o mínimo de $0,456$.

### Passo 4: Projeto do Compensador

Com os polos $s_d$ definidos, calculamos os parâmetros do compensador $C(s) = \frac{K_c(s+z_c)}{s}$ usando os critérios de ângulo e magnitude.

1.  **Cálculo do Zero do Compensador ($z_c$):**
    -   **Lógica:** O zero $z_c$ deve ser posicionado de forma que a deficiência de fase do sistema em malha aberta no ponto $s_d$ seja compensada, satisfazendo a condição de ângulo: $\arg(C(s_d)g_{red}(s_d)) = -180^\circ$.
    -   **Cálculo:** Após calcular a fase de $g_{red}(s_d)$ e do integrador em $s_d$, determinamos que a fase a ser fornecida pelo termo $(s_d+z_c)$ é de aproximadamente $83,4^\circ$. Geometricamente, isso posiciona o zero em **$z_c \approx 0,5$**.

2.  **Cálculo do Ganho do Compensador ($K_c$):**
    -   **Lógica:** O ganho $K_c$ é ajustado para garantir que o LDR passe exatamente pelo ponto $s_d$, satisfazendo a condição de magnitude: $|C(s_d)g_{red}(s_d)| = 1$.
    -   **Cálculo:** Substituindo $s_d$ e $z_c$ na equação, encontramos o ganho **$K_c \approx 0,85$**.

-   **Compensador Projetado para o Modelo Reduzido:**
    $$
    C_{red}(s) = \frac{0,85(s+0,5)}{s}
    $$

### Passo 5: Verificação do Desempenho e Análise dos Critérios

Para verificar se o projeto atende aos requisitos, simulamos a resposta ao degrau do sistema em malha fechada.

**INSERIR_GRAFICO_AQUI**

A análise da simulação revela o seguinte:

-   **Erro Estático:** **Critério Cumprido.** A saída do sistema converge para 1, resultando em erro estático nulo, como esperado devido à inclusão do integrador no compensador.
-   **Tempo de Acomodação:** **Critério Cumprido.** O tempo de acomodação (critério de 5%) observado na simulação é de aproximadamente 7.2s, que é inferior aos 10s exigidos.
-   **Overshoot:** **Critério NÃO Cumprido.** O overshoot medido na simulação é de aproximadamente **24.5%**, que é **superior** ao máximo de 20% permitido.

#### **Por que o Overshoot não foi Cumprido?**

Apesar de os polos dominantes terem sido alocados em uma posição que teoricamente garantiria um overshoot inferior a 20%, o **zero do compensador ($z_c=0,5$)** também influencia a resposta do sistema.

-   **Efeito de Proximidade do Zero:** O zero do compensador, $s=-0,5$, aparece no numerador da função de transferência de malha fechada. Por estar relativamente próximo da origem, ele introduz uma componente derivativa na resposta, o que tende a **aumentar a velocidade da resposta e, consequentemente, o overshoot**. A teoria baseada apenas nos polos dominantes não leva em conta esse efeito adicional, o que explica a discrepância entre o overshoot esperado ($\zeta=0,6 \implies M_p \approx 9,5\%$) e o overshoot real (24.5%).

Portanto, o projeto com o modelo reduzido conseguiu atender aos critérios de erro e tempo de acomodação, mas falhou no requisito de overshoot devido ao efeito do zero do compensador. Para um projeto mais preciso, seria necessário escolher um polo dominante com um fator de amortecimento ainda maior para compensar esse efeito, ou utilizar uma técnica de projeto diferente.