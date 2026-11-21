# REPOSITORIOS_DE_ESTUDO
Auditoria de Cybersecurity | DIO Santander
Projeto prático de segurança da informação  
Data: Novembro de 2026

1. Introdução

Este projeto apresenta uma prática de auditoria de segurança desenvolvida como parte do desafio da DIO.

O objetivo foi utilizar ferramentas do Kali Linux para identificar serviços expostos, analisar possíveis vulnerabilidades e testar mecanismos de autenticação em um ambiente virtual controlado.
2. Ambiente utilizado
| Componente | Configuração |
| Virtualizador | Oracle VirtualBox |
| Atacante | Kali Linux |
| Alvo | Metasploitable 2 |
| IP | 192.168.56.101 |
| Rede | Host-only isolada |
| Ferramentas | Nmap, Hydra, Medusa, FTP e cURL |
Todos os testes foram realizados em laboratório, sem acesso a sistemas externos.

3. Reconhecimento com Nmap
Comando utilizado:
nmap -sV -n -p 21,22,80,445,139 192.168.56.101
Foram identificadas cinco portas abertas:
| Porta | Serviço | Versão |
| 21 | FTP | vsftpd 2.3.4 |
| 22 | SSH | OpenSSH 4.7p1 |
| 80 | HTTP | Apache 2.2.8 |
| 139 | NetBIOS | Samba |
| 445 | SMB | Samba |
As versões encontradas são antigas e apresentam possíveis riscos de segurança.

Figura 1 — Resultado da varredura Nmap.

4. Análise do FTP

Foi identificada a versão vsftpd 2.3.4, associada historicamente à vulnerabilidade CVE-2011-2523.
Também foram realizados testes de autenticação e identificação do serviço.
Comando utilizado:
nmap -sV -p 21 --script ftp-syst 192.168.56.101
O acesso anônimo foi aceito pelo servidor, retornando:
230 Login successfu

5. Análise do serviço Web
Foi identificado o Apache 2.2.8 com PHP 5.2.4.
Comandos utilizados:
nmap -sV -n -p 80 192.168.56.101
curl -I http://192.168.56.101
A página inicial apresentava aplicações como phpMyAdmin, Mutillidae, DVWA e WebDAV.
Na análise do DVWA, foi identificado um redirecionamento HTTP 302 para `login.php`, além dos cookies PHPSESSID e security=high.

6. Inspeção dos arquivos
Com as credenciais locais do laboratório (`msfadmin:msfadmin`), foi realizada uma inspeção dos diretórios da aplicação
hostname && pwd && ls
cd /var/www/dvwa && ls -l config/
Foi observado que o arquivo `config.inc.php` possuía permissão de leitura global (`-rw-r--r--`), representando um possível risco de exposição de informações sensíveis.

7. Testes com Hydra e Medusa
Foram criadas listas de usuários e senhas para avaliar o comportamento da autenticação do DVWA.
O Hydra realizou 16 tentativas, mas não encontrou credenciais válidas.
Resultado:
0 valid password found
O Medusa apresentou erros relacionados ao redirecionamento HTTP 302 e ao gerenciamento de sessão da aplicação.

Análise
Os resultados demonstraram duas dificuldades:

- Hydra: as listas utilizadas não continham a combinação real de credenciais do DVWA (`admin:password`).
- Medusa: apresentou dificuldades para interpretar as respostas HTTP e gerenciar a sessão da aplicação.

8. Riscos identificados
| Problema | Impacto |
| vsftpd 2.3.4 associado à CVE-2011-2523 | Crítico |
| Acesso anônimo ao FTP | Médio/Alto |
| Apache e PHP desatualizados | Alto |
| Controles de autenticação insuficientes no cenário | Médio |
| Permissões amplas em arquivo de configuração | Médio |

9. Recomendações
- Atualizar os serviços e corrigir vulnerabilidades conhecidas.
- Desabilitar o acesso anônimo ao FTP.
- Utilizar protocolos criptografados.
- Implementar bloqueio de tentativas excessivas de autenticação.
- Adotar senhas fortes e autenticação multifator.
- Restringir permissões de arquivos sensíveis.
- Monitorar registros de acesso e autenticação.

10. Conclusão
O laboratório permitiu praticar conceitos de reconhecimento de redes, análise de serviços, identificação de vulnerabilidades e testes de autenticação.
A utilização do Nmap, Hydra e Medusa ajudou a compreender melhor o funcionamento dessas ferramentas e a importância de interpretar seus resultados.
Mesmo sem obter credenciais válidas nos testes automatizados, foi possível identificar limitações relacionadas às listas utilizadas e ao gerenciamento de sessões HTTP.
Este projeto representa uma etapa prática da minha formação em cibersegurança e será utilizado como base para estudos e auditorias futuras em ambientes con
