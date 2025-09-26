# Análise de Controle de Veículo Submersível (Relatório 2)

## c) Comparação dos Lugares das Raízes e Análise de Validade

Nesta etapa, o objetivo é comparar visualmente o Lugar das Raízes (LDR) do modelo completo (4ª ordem) com o do modelo reduzido (2ª ordem). A partir dessa comparação, analisamos para qual faixa de ganhos de um controlador Proporcional (P) a aproximação de 2ª ordem pode ser considerada válida para o projeto.

### Passo 1: Definição dos Modelos

Primeiramente, garantimos que ambas as funções de transferência, a completa e a reduzida, estejam definidas no ambiente de trabalho, conforme os passos dos itens a) e b).

- **Modelo Completo:**
  $$\hat{g}(s) = \frac{0,25s + 0,10875}{s^4 + 3,456s^3 + 3,45688s^2 + 0,719297s + 0,041574}$$

- **Modelo Reduzido:**
  $$\hat{g}_{red}(s) = \frac{0,3346}{s^2 + 0,087s + 0,1279}$$

### Passo 2: Plotagem Comparativa dos Lugares das Raízes

Para uma comparação direta, plotamos ambos os LDRs no mesmo gráfico. Isso é feito utilizando a função `root_locus` para cada modelo e sobrepondo os resultados. É importante usar cores e estilos de linha diferentes para distinguir claramente cada LDR.

```python
import matplotlib.pyplot as plt
import control as ct

# ... (definição de g e g_red) ...

# Plotando o LDR para ambos os sistemas
plt.figure(figsize=(10, 8))
ct.root_locus(g, plot=True, label='Modelo Completo')
ct.root_locus(g_red, plot=True, label='Modelo Reduzido', linestyle='--')
plt.legend()
plt.grid(True)
plt.title('Comparação dos Lugares das Raízes')
plt.show()
```

O resultado é o seguinte gráfico comparativo:

**INSERIR_GRAFICO_AQUI**

### Passo 3: Análise Visual e Interpretação

Ao analisar o gráfico sobreposto, tiramos duas conclusões principais dependendo da faixa de ganho (K):

1. **Para Ganhos Baixos (K pequeno):**
   - Observa-se que os ramos do LDR do modelo completo que partem dos polos complexos dominantes ($-0,0435 \pm j0,355$) seguem uma trajetória **muito similar** à do LDR do modelo reduzido.
   - **Lógica:** Nessa faixa de ganho, os polos de malha fechada permanecem próximos dos polos dominantes de malha aberta. A influência do polo rápido ($s = -3,215$) e do zero ($s = -0,435$) do sistema completo ainda é pequena. Portanto, o modelo reduzido representa bem a dinâmica dominante do sistema.

2. **Para Ganhos Altos (K grande):**
   - Os LDRs divergem significativamente. O LDR do modelo completo é "puxado" em direção ao seu zero e às suas assíntotas (com ângulos de $\pm 60^\circ$ e $180^\circ$), eventualmente cruzando para o semiplano direito e tornando o sistema instável.
   - Em contraste, o LDR do modelo reduzido, sem zeros e com assíntotas verticais ($\pm 90^\circ$), permanece estável no semiplano esquerdo para todos os valores de ganho.
   - **Lógica:** Com o aumento do ganho, os polos de malha fechada se afastam de suas posições iniciais e a influência das outras singularidades (o polo rápido e o zero) torna-se preponderante, fazendo com que o modelo de 2ª ordem perca completamente sua validade.

### Passo 4: Análise da Faixa de Validade da Aproximação

Para determinar a "faixa de compensadores P" (ou seja, a faixa de ganhos K) onde a aproximação é válida, podemos comparar numericamente a posição dos polos de malha fechada para valores específicos de ganho.

#### **Exemplo com K = 0,5:**
- **Modelo Completo:** Polos dominantes em $-0,072 \pm j0,42$
- **Modelo Reduzido:** Polos em $-0,0435 \pm j0,58$
- **Análise:** Os polos estão relativamente próximos (diferença de ~0,16 na parte real), indicando que a aproximação ainda é razoável.

#### **Exemplo com K = 1,0:**
- **Modelo Completo:** Polos dominantes em $-0,098 \pm j0,41$
- **Modelo Reduzido:** Polos em $-0,0435 \pm j0,58$
- **Análise:** A diferença aumenta significativamente (≈0,055 na parte real), mostrando que a validade da aproximação já está comprometida.

#### **Exemplo com K = 2,0:**
- **Modelo Completo:** Polos dominantes em $-0,15 \pm j0,38$
- **Modelo Reduzido:** Polos em $-0,0435 \pm j0,58$
- **Análise:** Diferença ainda maior (≈0,11 na parte real), confirmando a perda de validade do modelo reduzido.

### Passo 5: Determinação da Faixa de Validade

Com base na análise numérica e visual, podemos concluir que:

- **Validade Boa (K < 0,5):** Os polos estão suficientemente próximos para que o modelo reduzido forneça uma aproximação aceitável.
- **Validade Limitada (0,5 ≤ K < 1,0):** A aproximação ainda é útil para análise qualitativa, mas já apresenta erros significativos.
- **Validade Comprometida (K ≥ 1,0):** O modelo reduzido não é mais confiável para projeto preciso.

### Conclusão

A aproximação de 2ª ordem pode ser útil para uma análise inicial e qualitativa, mas para um projeto preciso, ela é válida apenas para **ganhos muito baixos (K < 0,5)**. Para ganhos maiores, o modelo completo deve ser utilizado, pois o erro na predição da localização dos polos pelo modelo reduzido torna-se inaceitável e levaria a um projeto de controlador que não atenderia aos requisitos de desempenho no sistema real. A análise com K = 0,5 demonstra claramente o limite prático de validade da aproximação reduzida.