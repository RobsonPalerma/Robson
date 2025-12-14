
📦 Explicação do Banco MySQL
Sistema de Estoque – Mercado Fácil
🗄️ Banco de Dados: mercado_facil
CREATE DATABASE mercado_facil;


👉 É o container principal onde ficam todas as tabelas do sistema.

🧱 1️⃣ Tabela produto
🎯 Finalidade

Guarda as informações principais de cada produto em estoque.

📄 Estrutura
produto (
    id,
    codigo,
    quantidade,
    estoque_minimo,
    estoque_maximo,
    status,
    data_cadastro,
    data_atualizacao
)

🔍 Campo por campo
Campo	Explicação	HTML relacionado
id	Identificador único do produto	Interno
codigo	Código do produto (ex: PROD001)	Código do Produto
quantidade	Quantidade atual em estoque	Dashboard / Movimentação
estoque_minimo	Limite mínimo permitido	Estoque Mínimo
estoque_maximo	Limite máximo permitido	Estoque Máximo
status	Situação do estoque (OK, BAIXO, FORA)	Dashboard
data_cadastro	Data do cadastro do produto	—
data_atualizacao	Última atualização automática	Relatório
📌 Por que existe o campo status?

Para não calcular toda hora no sistema, o banco já informa:

OK → estoque normal

BAIXO → abaixo do mínimo

FORA → zerado

Isso deixa o sistema mais rápido e organizado.

🔄 2️⃣ Tabela movimentacao_estoque
🎯 Finalidade

Registra todas as entradas e saídas de produtos (histórico).

📄 Estrutura
movimentacao_estoque (
    id,
    produto_id,
    tipo,
    quantidade,
    motivo,
    referencia,
    data_movimentacao
)

🔍 Campo por campo
Campo	Explicação	HTML
produto_id	Produto movimentado	Selecionar Produto
tipo	Entrada ou saída	Tipo de Movimentação
quantidade	Quantidade movimentada	Quantidade
motivo	Compra, venda, perda, etc	Motivo
referencia	Nota fiscal, ajuste, etc	Referência
data_movimentacao	Data/hora da ação	Automático

📌 Nunca se altera a quantidade direto
➡ Sempre via movimentação (boas práticas de estoque).

⚠️ 3️⃣ Tabela alerta_estoque
🎯 Finalidade

Controla avisos automáticos quando o estoque está crítico.

📄 Estrutura
alerta_estoque (
    id,
    produto_id,
    tipo_alerta,
    descricao,
    resolvido,
    data_alerta
)

🔍 Campo por campo
Campo	Explicação
tipo_alerta	ESTOQUE_BAIXO ou FORA_DE_ESTOQUE
descricao	Mensagem exibida ao usuário
resolvido	Se o alerta já foi tratado
data_alerta	Quando o alerta surgiu

📌 Usado na aba Alertas do sistema.

📊 4️⃣ View vw_relatorio_estoque
🎯 Finalidade

Facilitar o relatório geral sem precisar escrever SQL complexo.

SELECT * FROM vw_relatorio_estoque;


📌 Ela já traz:

Código

Quantidade

Mínimo

Máximo

Status

Data de atualização

➡ Exatamente o que o HTML mostra no Relatório.

⚙️ 5️⃣ Trigger de Status do Produto
🎯 O que é Trigger?

É um código que roda automaticamente dentro do MySQL.

🔁 Quando ele roda?

Ao inserir

Ao atualizar um produto

🧠 Lógica
Se quantidade = 0 → FORA
Se quantidade < mínimo → BAIXO
Senão → OK


📌 Assim:

O sistema não precisa calcular status

O banco sempre está consistente

🔄 6️⃣ Trigger de Movimentação
🎯 Função

Atualiza o estoque automaticamente quando ocorre uma movimentação.

🧠 Lógica
Entrada → soma quantidade
Saída → subtrai quantidade


📌 Você só grava na tabela movimentacao_estoque
➡ O banco cuida do resto.

🔐 7️⃣ Relacionamentos (Integridade)

Um produto pode ter várias movimentações

Um produto pode ter vários alertas

ON DELETE CASCADE:

Se apagar um produto, apaga histórico e alertas

📌 Evita dados órfãos.
