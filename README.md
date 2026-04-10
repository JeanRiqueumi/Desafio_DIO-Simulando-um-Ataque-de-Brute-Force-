Auditoria de Segurança: Ataques de Força Bruta com Medusa
Sobre o Projeto
Este projeto foi desenvolvido como parte do desafio da DIO (Digital Innovation One). O objetivo é simular cenários de ataques de força bruta contra serviços de rede e aplicações web para compreender vulnerabilidades de autenticação e propor medidas de mitigação.

Ambiente de Laboratório
Para garantir um ambiente seguro e controlado, utilizei a seguinte estrutura:

Atacante: Kali Linux (VM)

Alvo: Metasploitable 2 (VM intencionalmente vulnerável)

Rede: Host-Only (Isolada do ambiente externo)

Ferramentas: nmap para reconhecimento e Medusa v2.3 para os ataques.

Processo de Execução
1. Reconhecimento e Varredura
O primeiro passo foi identificar os serviços ativos no alvo através de um scan de rede.

Bash
sudo nmap -sV 192.168.56.105
Observação: Identifiquei portas abertas como 21 (FTP), 80 (HTTP) e 445 (SMB), que serviram de vetores para os testes seguintes.

2. Ataque de Força Bruta em FTP
Utilizei o Medusa para testar uma lista de usuários e senhas contra o serviço vsftpd.

Comando: medusa -h 192.168.56.105 -U usuarios.txt -P senhas.txt -M ftp

Resultado: Sucesso na identificação das credenciais msfadmin:msfadmin.

3. Automação em Formulário Web (DVWA)
Simulei um ataque contra o formulário de login da aplicação DVWA. O diferencial aqui foi configurar o sinal de erro para que a ferramenta soubesse diferenciar um login falho de um sucesso.

Comando: medusa -h 192.168.56.105 -u admin -P senhas.txt -M web-form -m FORM:"dvwa/login.php" -m DENY-SIGNAL:"Login failed"
Nota Técnica: Troubleshooting no Módulo Web-Form
"Durante os testes no DVWA, foram identificados comportamentos de Falso Positivo e Falso Negativo. Isso ocorre devido à natureza volátil das respostas HTTP e à gestão de sessões (cookies) da aplicação alvo, que nem sempre retorna a string exata configurada no DENY-SIGNAL.

Resultado da Auditoria: Embora a ferramenta tenha reportado sucessos indevidos em certas configurações, a validação manual e o refinamento dos parâmetros permitiram concluir que a lista de senhas testada foi processada integralmente, demonstrando a importância do analista não confiar cegamente nos outputs automatizados."
Evidência:

4. Password Spraying em SMB
Diferente da força bruta tradicional, apliquei a técnica de password spraying testando uma única senha contra vários usuários possíveis, simulando uma tentativa de evitar bloqueios de conta.

Medidas de Mitigação (Blue Team)
Para proteger sistemas contra os ataques demonstrados, recomendo:

Políticas de Senhas: Implementar requisitos de complexidade e troca periódica.

Account Lockout: Bloquear temporariamente a conta ou o IP após um número específico de tentativas falhas (ex: Fail2Ban).

MFA (Multifactor Authentication): Fundamental para aplicações web e acessos administrativos.

Monitoramento de Logs: Configurar alertas para grandes volumes de tentativas de login em curto espaço de tempo.

Autor
Jean Canova | Estudante de Ciência da Computação | Entusiasta de Cybersecurity
