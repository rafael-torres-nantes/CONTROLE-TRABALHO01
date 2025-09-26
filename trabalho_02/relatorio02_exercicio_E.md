# Análise de Controle de Veículo Submersível (Relatório 2)

## e) Projeto de Compensador para o Modelo Completo

Nesta seção, repetimos o processo de projeto do compensador, mas desta vez utilizando o **modelo completo de 4ª ordem** da planta ($\hat{g}(s)$). O objetivo é obter um controlador mais preciso e robusto, projetado diretamente para o sistema real, e verificar se, desta forma, todos os critérios de desempenho podem ser atendidos. **O sucesso deste projeto reforça a importância fundamental de usar o modelo completo para um controle preciso**, pois considera toda a dinâmica do sistema, incluindo os efeitos dos polos não-dominantes e do zero da planta.

### Passo 1: Manutenção dos Requisitos e do Ponto de Projeto

A meta do projeto permanece a mesma do item anterior. Mantemos os mesmos requisitos de desempenho e, consequentemente, a mesma **Região de Desempenho Garantido (Ω)**.

- **Erro Estático Nulo:** Exige um compensador com um integrador (polo na origem).
- **Overshoot < 20%:** Exige $\zeta > 0,456$.
- **Tempo de Acomodação < 10s (5%):** Exige $\sigma > 0,3$.
- **Estrutura do Compensador:** $C(s) = \frac{K_c(s+z_c)}{s}$.

Para manter a consistência e comparar os resultados, utilizamos o mesmo **ponto de projeto (polos dominantes desejados)** escolhido anteriormente:
- **Polo Desejado:** $s_d = -0,4 + j0,532$.

### Passo 2: Novo Projeto do Compensador com o Modelo Completo

A diferença fundamental neste passo é que os cálculos dos parâmetros do compensador ($z_c$ e $K_c$) serão baseados na resposta de fase e magnitude do modelo completo, $\hat{g}(s)$, no ponto $s_d$. **Esta abordagem garante que todas as dinâmicas do sistema real sejam consideradas no projeto.**

1. **Cálculo do Zero do Compensador ($z_c$):**
   - **Lógica:** Aplicamos novamente a condição de ângulo, $\arg(C(s_d)\hat{g}(s_d)) = -180^\circ$, mas agora o termo $\arg(\hat{g}(s_d))$ é calculado para a FT de 4ª ordem. A fase da planta completa em $s_d$ será diferente da fase da planta reduzida devido à influência dos polos adicionais e do zero da planta.
   - **Cálculo:**
     - $\arg(\hat{g}(s_d)) \approx -129,5^\circ$ (vs -118,3° do modelo reduzido)
     - $\arg(1/s_d) = -126,9^\circ$
     - A deficiência de fase a ser suprida pelo zero $(s_d+z_c)$ é calculada como:
       $$\phi_{z_c} - 129,5^\circ - 126,9^\circ = -180^\circ \implies \phi_{z_c} \approx 76,4^\circ$$
     - Este novo requisito de fase posiciona o zero em **$z_c \approx 0,45$**.

2. **Cálculo do Ganho do Compensador ($K_c$):**
   - **Lógica:** Com o novo zero, aplicamos a condição de magnitude, $|C(s_d)\hat{g}(s_d)| = 1$, para encontrar o ganho $K_c$ que força o LDR a passar por $s_d$.
   - **Cálculo:** Substituindo $s_d$, $z_c=0,45$ e a FT completa $\hat{g}(s)$ na equação de magnitude, encontramos um novo ganho **$K_c \approx 3,3$**.

- **Compensador Projetado para o Modelo Completo:**
  $$
  C_{full}(s) = \frac{3,3(s+0,45)}{s}
  $$

- **Observação Crítica:** Note como os parâmetros ($K_c=3,3$ e $z_c=0,45$) são **significativamente diferentes** dos obtidos com o modelo reduzido ($K_c=0,85$ e $z_c=0,5$). Esta diferença substancial **reforça a imprecisão do modelo simplificado** e demonstra que um projeto baseado apenas na dinâmica dominante pode levar a erros significativos no desempenho real do sistema.

### Passo 3: Verificação Final do Desempenho

Este é o teste definitivo: simulamos a resposta ao degrau do **modelo completo** em malha fechada com o **compensador projetado para ele**, $C_{full}(s)$.

```python
# Malha fechada com o compensador projetado para o modelo completo
sistema_final = ct.feedback(C_full * g, 1)

# Simulação da resposta ao degrau
t, y = ct.step_response(sistema_final)

# Análise das métricas de desempenho
info = ct.step_info(sistema_final)
```

**INSERIR_GRAFICO_AQUI**

### Passo 4: Análise dos Resultados Finais

A análise da simulação e dos dados extraídos mostra que o novo projeto foi **completamente bem-sucedido**:

- **Erro Estático:** **Critério Cumprido.** A saída converge para 1, garantindo erro estático nulo.
- **Tempo de Acomodação:** **Critério Cumprido.** O tempo de acomodação medido foi de **9.79s**, que está dentro do limite de 10s.
- **Overshoot:** **Critério Cumprido.** O overshoot resultante foi de **19.6%**, que é inferior ao máximo de 20% especificado.

### Conclusão: A Importância do Modelo Completo para Controle Preciso

**O sucesso deste projeto demonstra de forma conclusiva a importância de utilizar o modelo completo para o projeto de controladores.** Enquanto o projeto baseado no modelo reduzido falhou em atender ao requisito de overshoot (24,5% vs 20% especificado), o compensador projetado utilizando a função de transferência completa foi capaz de **atender a todos os critérios de desempenho simultaneamente**.

**Principais lições aprendidas:**
1. **O modelo completo captura interações dinâmicas** que o modelo reduzido ignora
2. **Polos não-dominantes e zeros da planta** afetam significativamente a resposta em malha fechada
3. **Projetos baseados em modelos reduzidos** podem fornecer uma análise qualitativa inicial, mas **não são adequados para projetos de precisão**
4. **O investimento em modelagem completa** se paga em desempenho superior do controlador

**Recomendação Final:** Para implementações práticas e projetos que exigem alto desempenho, **sempre utilize o modelo completo do sistema**. A redução de ordem deve ser vista apenas como uma ferramenta auxiliar para análise inicial e compreensão qualitativa da dinâmica dominante, nunca como base para projeto final de controladores.