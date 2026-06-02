# Unificação de Bases de Microdados — Educação Superior

Projeto de integração dos microdados do **ENADE** e do **Censo da Educação Superior**, com o objetivo de consolidar informações sobre o ensino superior brasileiro em uma base única.

---

## Notebooks

### 01 · Compilação do ENADE
**`compilar_arquivos_enade.ipynb`**

O ENADE é distribuído em 33 arquivos separados, onde apenas o arquivo 1 contém o código da instituição (`CO_IES`). Este notebook realiza a compilação horizontal de todos os arquivos em uma base única.

**O que faz:**
- Carrega o arquivo 1 como base inicial — único que contém `CO_IES`, `CO_CURSO` e `CO_MUNIC_CURSO`
- Ordena todos os arquivos pelo `CO_CURSO` antes de unir, garantindo o alinhamento correto das linhas
- Une os arquivos horizontalmente com `concat(axis=1)`, adicionando apenas colunas novas a cada iteração
- Ignora automaticamente colunas duplicadas entre arquivos (ex: `NU_ANO` que aparece em todos)

**Configurações disponíveis:**
| Constante | Descrição |
|---|---|
| `ARQUIVOS_INCLUIR` | Lista de arquivos a incluir — vazio para incluir todos |
| `ARQUIVOS_IGNORAR` | Lista de arquivos a ignorar — vazio para ignorar nenhum |
| `COLUNAS_INCLUIR` | Lista de colunas a incluir — vazio para incluir todas |
| `COLUNAS_IGNORAR` | Lista de colunas a ignorar — vazio para ignorar nenhuma |

> Em cada linha contém informações de desempenho e características do aluno, porém não é possível identificar o perfil de um aluno específico.

---

### 02 · Agrupamento do ENADE por Curso e Município
**`agrupamento_curso_enade.ipynb`**

A base compilada tem uma linha por aluno. Para viabilizar a junção com o Censo — cuja menor granularidade é o curso por município — este notebook agrega os dados por `CO_CURSO` + `CO_MUNIC_CURSO`, gerando perfis estatísticos por curso.

**Chave de agrupamento:**
```python
CHAVE = ['CO_CURSO', 'CO_MUNIC_CURSO']
```

**Funções de agregação disponíveis:**

| Função | Uso |
|---|---|
| `agregar_valor_unico` | Variáveis fixas do curso |
| `agregar_contagem_categorica` | Contagem de todas as categorias |
| `agregar_contagem_categorias_selecionadas` | Contagem de categorias específicas com nomes personalizados |
| `agregar_contagem_por_nivel` | Distribuição de notas em 4 faixas (0-25, 26-50, 51-75, 76-100) |
| `agregar_media` | Média de variável numérica |

---

### 03 · Join Censo + ENADE
**`join_censo_enade.ipynb`**

Une a base do Censo da Educação Superior com o ENADE agrupado pela chave composta de curso e município.

**O que faz:**
- Realiza `left join` garantindo que todos os cursos do Censo aparecem na base final
- Remove colunas duplicadas geradas pelo merge (`CO_IES_x/y`, `CO_MUNIC_CURSO`)

## Tecnologias Utilizadas

- **Python 3.12**
- **pandas** — manipulação e agregação dos dados
- **glob** — busca automática de arquivos
- **os / re** — manipulação de caminhos e ordenação numérica de arquivos
- **Google Colab** — ambiente de desenvolvimento
- **Google Drive** — armazenamento dos dados e notebooks
