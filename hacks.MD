# Comandos úteis

## Importar base quando usando WSL2
```
docker exec -i <container_id> mariadb -udefault -psecret default < /mnt/c/Users/Rochele/Downloads/database.sql
```

## Importar base já modificando ela
```
# 1 - nome do banco
# 2 - nome do arquivo sql
# 3 - id do container
function fumadocker() {
    FILE=/mnt/c/Users/Rochele/Downloads/$2.sql
    if [ ! -f "$FILE" ]; then
        echo "Arquivo $2.sql não existe"
    else
        echo "Iniciando importação para $1"
    	echo 'Criando banco vazio...'
    	docker exec -i $3 mariadb -udefault -psecret -e "DROP DATABASE IF EXISTS $1; CREATE DATABASE $1;"
    	echo 'OK'
    	echo 'Configurando mysql...'
    	docker exec -i $3 mariadb -udefault -psecret -e 'SET GLOBAL max_allowed_packet=1073741824;'
    	docker exec -i $3 mariadb -udefault -psecret -e 'SET @@GLOBAL.group_concat_max_len = 4294967295;'
    	docker exec -i $3 mariadb -udefault -psecret -e 'SET FOREIGN_KEY_CHECKS = 0;'
        docker exec -i $3 mariadb -udefault -psecret -e 'SET @@GLOBAL.innodb_buffer_pool_size = 3000000000;'
        echo 'OK'
    	echo 'Importando dados do arquivo...'
    	docker exec -i $3 mariadb -udefault -psecret default < FILE
    	echo 'OK'
    	echo 'Alterando senhas dos usuários para "a"...'
    	docker exec -i $3 mariadb -udefault -psecret -D $1 -e 'UPDATE users SET password = "$2y$10$tozlEAQSWlsAEWxbEwKa6.EGER4K3bpM0mq/VeJc4WRjVEPhZ535K";'
        echo 'Alterando domínio dos e-mails'
        docker exec -i $3 mariadb -udefault -psecret -e 'SET FOREIGN_KEY_CHECKS = 1;'
        docker exec -i $3 mariadb -udefault -psecret -e "SET @@sql_mode = 'STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';"
    	echo 'OK'
    fi
}
```

# Instalar tema para o ZSH

Execute o script de instalação do ZSH Tools + Pluguins
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ErickRock/oh-my-zsh-on-windows-terminal/master/tools-zsh-install.sh)" -y

```
Nessa seguinte tela entre com exit e tecle enter para o sript continuar.

## Instalando

```
sudo git clone https://github.com/denysdovhan/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt"
```

Crie o link simbólico
```
sudo ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
```
## Ativando o tema

Digite code code ~/.zshrc e tecle enter que abrirá o VS Code para editar o arquivo, edite o campo `ZSH_THEME="fino"` para `ZSH_THEME="spaceship"` e salve. Pronto, você acabou de mudar o tema.

## Pluguins inclusos

Esses são os plugins inclusos e abaixo explico como funciona cada um deles:
- **zdharma/fast-syntax-highlighting**: Adiciona syntax highlighting na hora da escrita de comandos que facilita principalmente em reconhecer comandos que foram digitados de forma incorreta.
- **zsh-users/zsh-autosuggestions**: Sugere comandos baseados no histórico de execução conforme você vai digitando.
- **zsh-users/zsh-completions**: Adiciona milhares de completitions para ferramentas comuns como Yarn, Homebrew, NVM, Node, etc, para você precisar apenas apertar TAB para completar comandos.

Obs.: ainda jogo isso autmático no Laradock :D
