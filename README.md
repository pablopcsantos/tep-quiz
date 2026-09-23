# TEP Quiz

Aplicação web educacional para preparação e treinamento para o exame de Título de Especialista em Pediatria (TEP), com geração de testes personalizados, realização de provas completas por ano, correção de questões objetivas, autoavaliação de questões discursivas, histórico e estatísticas armazenados localmente no navegador.

O projeto é executado inteiramente no navegador e não depende de backend ou banco de dados remoto. As questões são mantidas no arquivo `banco_questoes.js`, enquanto histórico, preferências de aparência e informações de revisão são armazenados localmente pelo navegador (`localStorage`).

## Principais funcionalidades

- Geração de testes por **temas/tags**.
- Seleção de questões **discursivas**, **objetivas** ou de ambos os tipos.
- Modo **Prova completa**, capaz de carregar todas as questões associadas a uma ou mais provas TEP selecionadas.
- Área específica **Provas completas**, separada dos filtros temáticos.
- Reconhecimento automático das provas cadastradas por tags no padrão `TEP 20XX`.
- Correção automática de questões objetivas quando o gabarito identifica uma alternativa válida.
- Autoavaliação de questões discursivas em **correta**, **parcialmente correta** ou **errada**.
- Exibição opcional das tags/temas durante a resolução.
- Suporte a informações complementares exibidas após a correção.
- Seleção ponderada de questões, reduzindo temporariamente a chance de repetição de questões vistas recentemente.
- **Contador regressivo** com três modos:
  - sem contador;
  - tempo por questão, com navegação sequencial;
  - tempo total de prova, com navegação livre.
- Histórico de testes e estatísticas de desempenho.
- Seleção e exclusão de testes do histórico, com confirmação por **Gravar alterações**.
- Exportação e importação de backup do histórico.
- Tema claro/escuro e padrões de fundo, incluindo patterns médicos com cápsulas e maleta clínica.
- Página **Sobre o projeto** com informações de autoria e desenvolvimento.
- Inclusão de novas questões com validação estrutural antes da mesclagem e geração de um novo arquivo `banco_questoes.js` atualizado.

## Provas completas

As tags que identificam uma prova, como:

```text
TEP 2025
TEP 2024
TEP 2023
```

continuam armazenadas dentro do campo `temas` das questões, preservando a compatibilidade com o banco existente. Entretanto, na tela **Configurar Novo Teste**, elas são tratadas separadamente das tags que descrevem conteúdo médico.

Ao selecionar uma ou mais opções em **Provas completas**:

1. todas as marcações de **Filtro de Temas (Tags)** são desmarcadas;
2. o tipo de questão é alterado automaticamente para **Prova completa**;
3. os campos de quantidade de questões deixam de ser utilizados;
4. todas as questões associadas às provas selecionadas são incluídas no teste, independentemente de serem objetivas ou discursivas.

Se mais de uma prova for selecionada, o teste reúne as questões de todas elas, sem duplicar uma mesma questão caso ela possua mais de uma tag de prova.

A identificação das provas é dinâmica: qualquer tag que siga o padrão `TEP 20XX` passa automaticamente a aparecer na área **Provas completas**.

## Diferença entre prova e tema

O campo `temas` do banco pode conter informações de naturezas diferentes. Por exemplo:

```javascript
{
    "temas": ["TEP 2025", "Pneumologia Pediátrica", "Asma"]
}
```

Nesse objeto:

- `TEP 2025` identifica a **prova/origem da questão**;
- `Pneumologia Pediátrica` e `Asma` identificam o **conteúdo médico**.

A interface separa essas duas finalidades sem exigir alteração do formato atual do banco. Assim, as tags de prova são exibidas em **Provas completas**, enquanto as demais permanecem em **Filtro de Temas (Tags)**.

## Estrutura do projeto

```text
TEP-Quiz/
├── index.html
├── banco_questoes.js
├── README.md
└── Arquivo/
    ├── Código de Exemplo.txt
    └── Orientações.txt
```

### `index.html`

Contém a interface e a lógica principal da aplicação: configuração dos testes, execução do quiz, cronômetros, correção, estatísticas, histórico, aparência e atualização do banco de questões.

### `banco_questoes.js`

Contém o array global `bancoDeQuestoes` com as questões utilizadas pelo sistema.

## Estrutura das questões

### Questão discursiva

```javascript
{
    "pergunta": "Texto da pergunta...",
    "gabarito": "Texto do gabarito...",
    "tipo": "discursiva",
    "temas": ["TEP 2025", "Pneumologia Pediátrica"]
}
```

### Questão objetiva

```javascript
{
    "pergunta": "Texto da pergunta...",
    "opcoes": [
        "Alternativa 1",
        "Alternativa 2",
        "Alternativa 3",
        "Alternativa 4"
    ],
    "gabarito": "Opção B",
    "tipo": "objetiva",
    "temas": ["TEP 2025", "Pneumologia Pediátrica"],
    "informacoesComplementares": "Conteúdo opcional exibido após a correção."
}
```

O sistema mantém compatibilidade com questões antigas que utilizem o campo singular `tema`, embora o formato preferencial seja o array `temas`.

## Seleção ponderada e revisão

Nos testes gerados por temas, o sistema atribui menor peso de sorteio às questões vistas recentemente. Questões registradas como vistas nos últimos sete dias recebem peso reduzido, diminuindo a probabilidade de repetição imediata sem eliminá-las completamente do sorteio.

O modo **Prova completa** não utiliza esse sorteio, pois sua finalidade é incluir integralmente todas as questões pertencentes às provas selecionadas.

## Histórico e dados locais

A aplicação utiliza `localStorage` para armazenar informações no próprio navegador, incluindo:

- histórico de testes;
- registro de quando cada questão foi vista;
- tema claro/escuro;
- padrão de fundo.

Como esses dados pertencem ao navegador e à origem em que o aplicativo é executado, limpar os dados do site ou trocar de navegador/dispositivo pode apagar o histórico local. O recurso de backup pode ser utilizado para preservar e transportar os dados de desempenho.

## Como executar

Não é necessário instalar dependências nem executar um servidor de aplicação.

1. mantenha `index.html` e `banco_questoes.js` na mesma pasta;
2. abra `index.html` em um navegador moderno;
3. para disponibilização online, os arquivos estáticos podem ser publicados, por exemplo, por meio do GitHub Pages.

Navegadores baseados em Chromium oferecem suporte mais amplo aos recursos opcionais de salvamento de arquivos. Quando a API específica não estiver disponível, a aplicação utiliza o mecanismo tradicional de download do navegador.

## Atualização do banco de questões

A tela **Atualizar Banco de Questões** permite colar novos objetos JavaScript/JSON, mesclá-los ao banco carregado e gerar um novo `banco_questoes.js`.

Antes de liberar a geração do novo arquivo, o sistema agora executa uma **validação estrutural e semântica** das questões coladas.

### O que é validado

Entre as verificações realizadas, estão:

- se cada item realmente é um objeto de questão;
- se `pergunta`, `gabarito` e `tipo` existem e não estão vazios;
- se `tipo` é exatamente `objetiva` ou `discursiva`;
- se `temas` é um array contendo pelo menos uma tag válida;
- se `informacoesComplementares`, quando presente, é texto;
- se questões objetivas possuem `opcoes` em formato de array;
- se o array `opcoes` contém entre 2 e 6 alternativas não vazias e não duplicadas;
- se o `gabarito` das objetivas aponta para uma alternativa válida (`Opção A` até `Opção F`, ou apenas a letra correspondente);
- se há indícios de perguntas duplicadas no lote colado ou no banco já existente;
- se uma tag iniciada por `TEP` segue o padrão `TEP 20XX`.

Se houver **erros**, a mesclagem é bloqueada. Se houver apenas **avisos**, a atualização continua possível, mas o sistema informa os pontos a revisar.

## Como criar o código para inserir novas questões

A área **Atualizar Banco de Questões** aceita um único objeto ou um array de objetos em formato JavaScript/JSON.

### Exemplo de questão discursiva

```javascript
{
    "pergunta": "Sobre a asma na criança, qual a conduta para exacerbação leve?",
    "gabarito": "Uso de beta-2 agonista de curta duração (ex: Salbutamol).",
    "tipo": "discursiva",
    "temas": ["TEP 2025", "Pneumologia Pediátrica", "Asma"]
}
```

### Exemplo de questão objetiva

```javascript
{
    "pergunta": "Qual o agente etiológico mais comum da bronquiolite viral aguda?",
    "opcoes": [
        "Rinovírus",
        "Vírus Sincicial Respiratório (VSR)",
        "Adenovírus",
        "Metapneumovírus"
    ],
    "gabarito": "Opção B",
    "tipo": "objetiva",
    "temas": ["TEP 2025", "Pneumologia Pediátrica", "Bronquiolite"],
    "informacoesComplementares": "<strong>Comentário:</strong> O VSR é a principal causa de bronquiolite em lactentes."
}
```

### Exemplo de lote com múltiplas questões

```javascript
[
    {
        "pergunta": "Texto da questão 1...",
        "gabarito": "Texto do gabarito 1...",
        "tipo": "discursiva",
        "temas": ["TEP 2024", "Pediatria Geral"]
    },
    {
        "pergunta": "Texto da questão 2...",
        "opcoes": ["Alternativa 1", "Alternativa 2", "Alternativa 3", "Alternativa 4"],
        "gabarito": "Opção C",
        "tipo": "objetiva",
        "temas": ["TEP 2024", "Neonatologia"]
    }
]
```

### Regras práticas para montar o código

1. Use aspas duplas ou simples de forma consistente.
2. Não deixe vírgulas faltando entre os campos.
3. Em questões objetivas, **não** escreva as letras `A)`, `B)` etc. dentro do texto das opções, a menos que isso faça parte do próprio enunciado da alternativa.
4. Prefira usar `temas` em vez de `tema`.
5. Para associar a questão a uma prova completa, inclua uma tag no padrão `TEP 20XX` dentro de `temas`.
6. Se a questão objetiva estiver anulada, o sistema aceitará o item, mas avisará que a correção permanecerá manual.

## Tecnologias

- HTML5
- CSS3
- JavaScript (Vanilla JS)
- Web Storage API (`localStorage`)
- File System Access API quando disponível, com fallback para download tradicional

O projeto não requer frameworks JavaScript, servidor de aplicação ou banco de dados externo.

## 👤 Autoria e desenvolvimento

Aplicação web educacional desenvolvida de forma independente por **Pablo Phillipe Cândido dos Santos**, destinada à preparação e ao treinamento para o exame de Título de Especialista em Pediatria (TEP), com questões, provas completas e testes personalizados executados diretamente no navegador.

O desenvolvimento contou com a utilização de ferramentas de inteligência artificial generativa como recurso auxiliar no processo de desenvolvimento, mantendo-se sob responsabilidade do autor a concepção, implementação, integração e verificação do projeto.

Currículo Lattes: [http://lattes.cnpq.br/9500873674712528](http://lattes.cnpq.br/9500873674712528)

## Observação

O projeto possui finalidade educacional e de treinamento. A organização e a apresentação das questões no aplicativo não implicam vínculo oficial com instituições responsáveis por provas, certificações ou títulos profissionais.
