# Instalação Husky e Commitlint

## Instalar o husky e commitlint

```
npm install husky --save-dev
npm install @commitlint/config-conventional @commitlint/cli --save-dev
```

## Configurar commitlint

```
touch commitlint.config.js && echo "module.exports = {
  extends: ['@commitlint/config-conventional'],
};" > commitlint.config.js
```

## Configurar o husky

Execute o comando: 
```
npx husky init
```
Adicione ao o script de lint ou formatação de sua preferencia ao hook pre-commit

`.husky/pre-commit`
```
npm run format
```

Crie o arquivo `commit-msg` na pasta `.husky`
```
touch .husky/commit-msg
```

Adicione o código abaixo no arquivo `commit-msg` para formatar os seus commits de acordo com as regras impostas abaixo `Esse arquivo você pode adicionar regras de acordo com o que for necessário ou até mesmo mudar a formatação final do commit`

```
#!/bin/bash

# Lê a mensagem do commit
commit_message=$(cat "$1")

# Define as palavras permitidas para a primeira palavra do commit
allowed_prefixes=("feat" "fix" "wip" "chore")

# Extrai a primeira palavra da mensagem do commit
first_word=$(echo "$commit_message" | awk '{print $1}')

# Verifica se a primeira palavra está na lista de palavras permitidas
if [[ ! " ${allowed_prefixes[@]} " =~ " ${first_word} " ]]; then
  echo "❌ O commit falhou: O prefixo da mensagem deve ser uma das seguintes palavras-chave: feat, fix, wip, chore."
  exit 1
fi

# Verifica se a mensagem do commit tem mais de 10 palavras
word_count=$(echo "$commit_message" | wc -w)
if [ "$word_count" -gt 10 ]; then
  echo "❌ O commit falhou: a mensagem não pode ter mais de 10 palavras. Você usou $word_count palavras."
  exit 1
fi

# Obtém o branch atual
current_branch=$(git rev-parse --abbrev-ref HEAD)

# Remove a primeira palavra da mensagem do commit
resto_da_mensagem=$(echo "$commit_message" | cut -d' ' -f2-)

# Formata a nova mensagem
formatted_message="${first_word}(${current_branch}): ${resto_da_mensagem}"

# Substitui a mensagem do commit pela nova mensagem formatada
echo "$formatted_message" > "$1"

# Executa o Commitlint para verificar a nova mensagem
npx --no-install commitlint --edit "$1"

# Mensagem válida
exit 0
```

## Habilite as alterações do husky no seu projeto

```
chmod +x .husky/commit-msg
```









