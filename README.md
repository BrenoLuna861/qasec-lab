# QASec Lab — Hackeando e blindando APIs

Laboratório prático do minicurso **QASec** (E-info 2026, FACAPE), sobre testes de segurança em APIs REST na visão de QA e de desenvolvimento.

### ▶️ [Abrir o laboratório](https://brenoluna861.github.io/qasec-lab/) — nada para instalar

A turma explora duas falhas do [OWASP API Security Top 10 (2023)](https://owasp.org/API-Security/editions/2023/en/0x11-t10/) e depois vê a correção funcionando:

| Falha | O ataque | A correção |
|---|---|---|
| **API1 — BOLA** (Broken Object Level Authorization) | trocar o ID na URL e ler a fatura de outra pessoa | buscar pelo ID **e** pelo dono vindo do token |
| **API3 — BOPLA** (Broken Object Property Level Authorization) | mandar `"role": "administrator"` no PATCH do perfil | whitelist com só os campos permitidos |

> ⚠️ **Projeto propositalmente vulnerável, só para fins didáticos.** Atacar sistemas sem autorização é crime no Brasil (Lei 12.737/2012).

## Como funciona

O `index.html` é o laboratório inteiro numa página só:

- **Cliente HTTP no estilo do Postman:** método, URL, Bearer Token, Body e resposta com o significado de cada status.
- **API vulnerável rodando dentro do navegador:** usuários, faturas e rotas de verdade, com JWT assinado em HMAC-SHA256. Um token adulterado recebe `401`.

Foi feito para laboratórios com computadores fracos, onde o Postman não roda. Funciona no celular e até offline: é só baixar e abrir o arquivo.

## Como testar

1. Abra o [laboratório](https://brenoluna861.github.io/qasec-lab/).
2. Clique em **Send**: o login do `aluno01` já vem pronto. Depois clique em **Usar este token**.
3. Siga as missões da esquerda:
   - **Desafio 01 (BOLA):** descubra seu ID, liste suas faturas e tente abrir a fatura de outra pessoa trocando o número em `/api/faturas/<id>`.
   - **Desafio 02 (BOPLA):** atualize seu perfil com `PATCH /api/users/me` e tente se promover a administrador mandando um campo extra.
4. Cada ataque bem-sucedido acende uma **flag** no topo (`BOLA`, `BOPLA` e a escondida `ADMIN`).
5. Clique em **Ligar modo seguro** e repita os ataques: agora eles devem dar `403` e `400`. O painel **"O código que respondeu"** mostra a linha que decidiu cada resposta, antes e depois da correção.

**Contas:** `aluno01@qasec.lab` até `aluno60@qasec.lab`, senha `qasec123`.

Os dados ficam só no navegador de cada pessoa e voltam ao padrão ao recarregar a página.

## As correções

**BOLA** — a trava fica na própria consulta:

```js
const fatura = await db.Faturas.findOne({
  _id: req.params.id_fatura,   // o que veio na URL
  ownerId: req.user.id_jwt     // o ID real, vindo do token assinado
});
if (!fatura) return res.status(403).json({ error: "Acesso Negado" });
```

**BOPLA** — só entra o que pode mudar:

```js
const dadosSeguros = {
  nome: req.body.nome,
  telefone: req.body.telefone
};
await db.Users.update(req.user.id, dadosSeguros);
```

## Publicando a sua cópia

Faça um fork, vá em **Settings › Pages**, escolha **Deploy from a branch › main › / (root)** e salve. Em um ou dois minutos o laboratório fica no ar em `https://<seu-usuario>.github.io/qasec-lab/`.

## 👤 Autor

**Breno Luna** — Ciência da Computação, FACAPE · [GitHub](https://github.com/BrenoLuna861) · [LinkedIn](https://www.linkedin.com/in/br%C3%AA/)

Minicurso apresentado com José Deividson no E-info 2026.

Licença [MIT](LICENSE).
