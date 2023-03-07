
Criando o pipeline de testes contínuos em Cypress

1. Criar um repositório com os testes automatizados
	1. Se necessário crie uma chave ssh para cadastrar no azure
		1. No terminal rode os comandos
		2. ` $ ssh-keygen `
			1. o sistema vai apontar a pasta onde o chave será gerada
		3. Copie o caminho e rode o comando 
			1. ` $ cat {o caminho apontado anteriormente}`
		4. Copie a chave 
		5. No azure, acesse "Manage SSH Keys"
		6. Clique em "+ New key"
			1. De um nome ao computador
			2. Cole a chave gerada no terminal
	2. Conecte a pasta dos seus testes com o repositório criado
		1. ` $ git remote add origin {link ssh}` 
		2. ` $ git push -u origin --all `
2. Entre em "Pipelines" >> "New pipeline" (Create Pipeline - se for a primeira a ser criada)
	1. ![[Pasted image 20230307100949.png]]
	2. Selecione a opção "Azure Repos Git" e selecione o repositório criado anteriormente
	3. Selecione "Node.js"
		1. No documento (azure.pipelines.yml)
			1. trigger: representa quando o teste vai ser executado (como padrão vem marcado para quando houver um push na branch main)
			2. pool: máquina que vai executar o teste na nuvem (como padrão vai ser executado em um ubuntu)
			3. steps: 
				1. task: NodeTool@0 (instala o node)
					1. o versionSpec pode ser alterado para '18.x'
			4. script: (instala as dependências do projeto +  build)
				1. Como o cypress não utiliza o build o código `npm run build` pode ser deletado e o "displayName" alterado para `npm install`
		2. Ao final do documento adicione o código:
	4. Clique em "Save and run"
```bash
- script: |
	npx cypress run
  displayName: 'run e2e tests'
  continueOnError: true
```


## Adicionando relatórios

1. No teste, adicione o seguinte código no arquivo "Cypress.config.js"
	1. Adicione o código antes do e2e
	2. Após isso pode rodar o comando `npx cypress open` para verificar se o cypress esta gerando o relatório
	3. ps - não esqueça de subir a alteração realizada para re positório
```js
  reporter: 'junit',
  reporterOptions: {
    mochaFile: 'results/my-test-output.xml',
    toConsole: true,
  },
```
3. Acesse a pipeline onde foi configurado os testes em Cypress
4. Clique em "Show assistent"
5. Buque por "Publish Test Results"
	1. Test result format : JUnit
	2. Teste results files: `**/output.xml`
	3. Marque a opção "Merge test results"
	4. Test run title : publish test results
	5. Clicar em "add" (não esqueça de selecionar a última linha do código antes de adicionar o comando)
	6. Clique em "Save"