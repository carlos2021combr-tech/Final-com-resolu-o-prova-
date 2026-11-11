# Respostas – Aula 11 (JSON, Web Storage e LocalStorage)

## Ordem dos scripts (etapa 4)
`dados.js` → `storage.js` → `app.js`. O `storage.js` usa `alunosIniciais` etc. (de `dados.js`) e o `app.js` usa `carregarDados()` e `STORAGE_KEYS` (de `storage.js`). Scripts comuns são executados na ordem em que aparecem; se a ordem mudar, ocorre `ReferenceError` (variável/função ainda não definida).

## Roteiro de console (etapas 5 a 22, 26 a 29, 47, 53, 61 e 63)
```js
localStorage.setItem("teste", "Funcionando"); console.log(localStorage.getItem("teste")); // Funcionando
localStorage.removeItem("teste");             console.log(localStorage.getItem("teste")); // null
const alunoTeste = { id: 1, nome: "Ana Souza", curso: "ADS", status: "ativo" };
localStorage.setItem("alunoTeste", alunoTeste);          // salva "[object Object]"
const alunoJSON = JSON.stringify(alunoTeste);            // typeof -> "string"
localStorage.setItem("alunoTeste", alunoJSON);
const alunoObjeto = JSON.parse(localStorage.getItem("alunoTeste")); console.log(alunoObjeto.nome); // Ana Souza
carregarDados("chave_inexistente", []);                  // [] (valor padrão)
localStorage.setItem(STORAGE_KEYS.alunos, "conteudo invalido"); // F5 -> catch, usa dados iniciais; depois restaurarAlunos()
sessionStorage.setItem("testeSessao", "ativo");
```

## Perguntas das etapas
- **11.** `setItem` só guarda **strings**; o objeto é convertido com `toString()` e vira `"[object Object]"`, perdendo os dados.
- **17.** Serialização = `JSON.stringify()` (objeto/array → string JSON).
- **18.** Desserialização = `JSON.parse()` (string JSON → objeto/array).
- **24.** O prefixo `academico_` evita colisão com outras chaves da mesma origem (outros sistemas/testes) e permite identificar e remover só as chaves da aplicação.
- **36.** O total pode ser calculado com `alunos.length`. Guardar outro valor duplicaria informação e poderia ficar inconsistente com o array.
- **38.** Formulário → Objeto → `push()` → `salvarDados()` → LocalStorage → `renderizarAlunos()`.
- **54.** 1) `try` envolve o código que pode falhar. 2) `catch` trata o erro sem derrubar a aplicação. 3) Sem tratamento, `JSON.parse` lançaria `SyntaxError`, o script pararia e a página ficaria sem dados/funcionalidades.
- **59.** Senhas, tokens, dados pessoais sensíveis (CPF, cartão, documentos). Qualquer script da mesma origem lê o LocalStorage.
- **62.** 1) Após F5, o `sessionStorage` **permanece**. 2) Ao fechar a aba, o `sessionStorage` **é apagado** (o `localStorage` continua). 3) Para este projeto, o **localStorage**.
- **65.** `removeItem()` apaga só a chave indicada; `clear()` apaga **todas** as chaves da origem, inclusive de outras partes do sistema.
- **69.** Não. Busca só muda o que é exibido, não os dados; salvar seria trabalho inútil e arriscaria gravar uma lista filtrada no lugar da completa.
- **71.** Cadastro → salvar · Remoção → salvar · Alteração → salvar · Busca → não salvar · Filtro → não salvar.

## Diagnósticos
- **77.** Salva `"[object Object],..."`: falta `JSON.stringify(alunos)`.
- **78.** `getItem` retorna string (ou `null`), e string não tem `push`: `TypeError`. É preciso `JSON.parse` (e tratar `null`).
- **79.** Lança `SyntaxError` e interrompe o script, a menos que haja `try...catch`.
- **80.** Chaves diferentes: o dado não é encontrado e `getItem` retorna `null`. Por isso usar `STORAGE_KEYS`.
- **81.** A tela mostra o aluno, mas ele só está na memória; após F5 ele desaparece (não foi gravado).

## Questões teóricas
1. **Persistência:** manter os dados disponíveis além da execução atual da página.
2. **Web Storage:** API do navegador que guarda pares chave-valor no cliente (`localStorage` e `sessionStorage`).
3. **LocalStorage × SessionStorage:** o primeiro permanece após fechar o navegador; o segundo dura só enquanto a aba estiver aberta.
4. **JSON:** JavaScript Object Notation, formato textual para dados estruturados.
5. `JSON.stringify()` converte objeto/array em string JSON.
6. `JSON.parse()` converte string JSON em objeto/array.
7. **Serialização:** transformar estrutura em texto (JSON).
8. **Desserialização:** transformar o texto JSON de volta em estrutura.
9. `setItem(chave, valor)` grava (ou substitui) um valor.
10. `getItem(chave)` lê o valor (string) ou `null`.
11. `removeItem(chave)` apaga uma chave.
12. `clear()` apaga todos os dados da origem, inclusive de outras partes do sistema.
13. Retorna `null` (por isso se trata a ausência com um valor padrão).
14. Evita erros de digitação, facilita manutenção e mudança de nomes em um só lugar.
15. Não alteram os dados, só a exibição; podem ser recalculados a qualquer momento.
16. Pode ser calculado a partir dos arrays; guardar duplicaria os dados.
17. Tratar erros: o `try` executa, o `catch` captura a falha (ex.: JSON inválido).
18. Tem limite de espaço (~5 MB), só guarda texto, não tem consultas, relações, segurança, nem acesso compartilhado entre usuários/dispositivos.
19. O conteúdo é legível por qualquer script da origem (inclusive XSS) e pelo usuário no DevTools; não é seguro.
20. `storage.js` concentra chaves e funções de salvar, carregar e remover, separando a persistência da interface.

## Regra de persistência no código
Toda alteração real (cadastro, remoção, alteração de status, restauração) chama `salvarDados()`; busca e filtro apenas leem o array.

## Observação técnica
Usei `copiarDados()` (cópia profunda via JSON) ao carregar/restaurar os iniciais. Com `[...alunosIniciais]` a cópia é rasa: ao alterar o status de um aluno, o objeto original também mudaria e "Restaurar" não restauraria.
