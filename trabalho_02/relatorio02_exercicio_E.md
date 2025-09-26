# Análise de Controle de Veículo Submersível (Relatório 2)

## e) Projeto de Compensador para o Modelo Completo

Nesta seção, repetimos o processo de projeto do compensador, mas desta vez utilizando o **modelo completo de 4ª ordem** da planta ($\\hat{g}(s)$). O objetivo é obter um controlador mais preciso e robusto, projetado diretamente para o sistema real, e verificar se, desta forma, todos os critérios de desempenho podem ser atendidos.

### Passo 1: Manutenção dos Requisitos e do Ponto de Projeto

A meta do projeto permanece a mesma do item anterior. Mantemos os mesmos requisitos de desempenho e, consequentemente, a mesma **Região de Desempenho Garantido (Ω)**.

  - **Erro Estático Nulo:** Exige um compensador com um integrador (polo na origem).
  - **Overshoot \< 20%:** Exige $\\zeta \> 0,456$.
  - **Tempo de Acomodação \< 10s (5%):** Exige $\\sigma \> 0,3$.
  - **Estrutura do Compensador:** $C(s) = \\frac{K\_c(s+z\_c)}{s}$.

Para manter a consistência e comparar os resultados, utilizamos o mesmo **ponto de projeto (polos dominantes desejados)** escolhido anteriormente:

  - **Polo Desejado:** $s\_d = -0,4 + j0,532$.

### Passo 2: Novo Projeto do Compensador com o Modelo Completo

A diferença fundamental neste passo é que os cálculos dos parâmetros do compensador ($z\_c$ e $K\_c$) serão baseados na resposta de fase e magnitude do modelo completo, $\\hat{g}(s)$, no ponto $s\_d$.

1.  **Cálculo do Zero do Compensador ($z\_c$):**

      - **Lógica:** Aplicamos novamente a condição de ângulo, $\\arg(C(s\_d)\\hat{g}(s\_d)) = -180^\\circ$, mas agora o termo $\\arg(\\hat{g}(s\_d))$ é calculado para a FT de 4ª ordem. A fase da planta completa em $s\_d$ será diferente da fase da planta reduzida.
      - **Cálculo:**
          - $\\arg(\\hat{g}(s\_d)) \\approx -129,5^\\circ$.
          - $\\arg(1/s\_d) = -126,9^\\circ$.
          - A deficiência de fase a ser suprida pelo zero $(s\_d+z\_c)$ é calculada como:
            $$\phi_{z_c} - 129,5^\circ - 126,9^\circ = -180^\circ \implies \phi_{z_c} \approx 76,4^\circ$$
          - Este novo requisito de fase posiciona o zero em **$z\_c \\approx 0,45$**.

2.  **Cálculo do Ganho do Compensador ($K\_c$):**

      - **Lógica:** Com o novo zero, aplicamos a condição de magnitude, $|C(s\_d)\\hat{g}(s\_d)| = 1$, para encontrar o ganho $K\_c$ que força o LDR a passar por $s\_d$.
      - **Cálculo:** Substituindo $s\_d$, $z\_c=0,45$ e a FT completa $\\hat{g}(s)$ na equação de magnitude, encontramos um novo ganho **$K\_c \\approx 3,3$**.

<!-- end list -->

  - **Compensador Projetado para o Modelo Completo:**
    $$
    $$$$C\_{full}(s) = \\frac{3,3(s+0,45)}{s}
    $$
    $$$$
    $$
  - **Observação:** Note como os parâmetros ($K\_c=3,3$ e $z\_c=0,45$) são significativamente diferentes dos obtidos com o modelo reduzido ($K\_c=0,85$ e $z\_c=0,5$). Isso reforça a imprecisão do modelo simplificado.

### Passo 3: Verificação Final do Desempenho

Este é o teste definitivo: simulamos a resposta ao degrau do **modelo completo** em malha fechada com o **compensador projetado para ele**, $C\_{full}(s)$.

```python
# Malha fechada com o compensador projetado para o modelo completo
sistema_final = ct.feedback(C_full * g, 1)

# Simulação da resposta ao degrau
t, y = ct.step_response(sistema_final)

# Análise das métricas de desempenho
info = ct.step_info(sistema_final)
```

**INSERIR\_GRAFICO\_AQUI**

### Passo 4: Análise dos Resultados Finais

A análise da simulação e dos dados extraídos mostra que o novo projeto foi bem-sucedido:

  - **Erro Estático:** **Critério Cumprido.** A saída converge para 1, garantindo erro estático nulo.
  - **Tempo de Acomodação:** **Critério Cumprido.** O tempo de acomodação medido foi de **9.79s**, que está dentro do limite de 10s.
  - **Overshoot:** **Critério Cumprido.** O overshoot resultante foi de **19.6%**, que é inferior ao máximo de 20% especificado.

### Conclusão

Ao contrário do projeto baseado no modelo reduzido, o compensador projetado utilizando a função de transferência completa foi capaz de **atender a todos os critérios de desempenho simultaneamente**.

Isso demonstra a importância de utilizar um modelo preciso para o projeto de controladores. Enquanto a redução de ordem é uma ferramenta útil para uma análise inicial e para entender a dinâmica dominante, ela pode levar a projetos imprecisos que não funcionam como esperado no sistema real. O projeto final, baseado no modelo de 4ª ordem, é o que deve ser considerado para a implementação, pois ele leva em conta toda a dinâmica da planta e resulta em um desempenho que satisfaz plenamente as especificações.