# Migração UNIX AIX local para Linux do Azure

Azure NetApp Files

Site Recovery

Banco de Dados SQL

Máquinas Virtuais

Rede Virtual

Esta solução descreve uma migração de uma plataforma IBM AIX Unix para Red Hat Enterprise Linux (RHEL) no Azure. O exemplo do mundo real era um aplicativo de Serviços humanos e de saúde para um cliente grande. O tempo de transação baixo e a latência eram requisitos importantes para os sistemas herdados e do Azure. Uma funcionalidade importante é armazenar informações do cliente em um banco de dados que se vincula a um armazenamento de arquivos de rede que contém imagens gráficas relacionadas. O Azure resolve essa necessidade com Azure NetApp Files.

Uma diferença distinta entre o sistema herdado e a implementação de nuvem é lidar com a segmentação de rede. As redes segmentadas do sistema herdado com firewalls. Uma plataforma de nuvem como o Azure segmenta redes com redes virtuais e grupos de segurança de rede que filtram o tráfego com base em vários critérios.

Outra diferença entre os sistemas são seus modelos de ALTA disponibilidade (HA) e DR (recuperação de desastre). No sistema herdados, a HA/DR usou principalmente backups e, até certo ponto, usou servidores redundantes no mesmo datacenter. Essa configuração forneceu dr., mas quase nenhuma funcionalidade de HA. Melhorar a HA/DR foi um fator importante para a mudança para a plataforma do Azure. O Azure usa clustering, armazenamento compartilhado e Azure Site Recovery para fornecer um alto nível de HA/DR.

## Possíveis casos de uso

Os principais fatores para a mudança do IBM AIX local para o RHEL no Azure podem incluir os seguintes fatores:

- **Hardware atualizado e custos reduzidos.** Componentes de hardware herdados locais estão continuamente desaparados e sem suporte. Os componentes de nuvem estão sempre atualizados. Os custos mensais podem ser menores na nuvem.
- **Ambiente de DevOps Agile.** A implantação de alterações de conformidade em um ambiente AIX local pode levar semanas. Talvez seja preciso configurar ambientes de engenharia de desempenho semelhantes muitas vezes para testar as alterações. Em um ambiente de nuvem do Azure, você pode configurar o UAT (teste de aceitação do usuário) e ambientes de desenvolvimento em horas. Você pode implementar alterações por meio de um pipeline de CI/CD (integração contínua e entrega contínua) moderno e bem DevOps definido.
- **BCDR (Continuidade dos Negócios e Recuperação de Desastre) aprimorada.** Em ambientes locais, os RTOs (objetivos de tempo de recuperação) podem ser longos. No ambiente AIX local de exemplo, o RTO por meio de backups e restaurações tradicionais foi de dois dias. A migração para o Azure reduziu o RTO para duas horas.

## Arquitetura

O diagrama a seguir mostra a arquitetura do sistema herdados do AIX local e pré-migração:

[![Diagrama que mostra a arquitetura do sistema AIX de pré-migração.](https://docs.microsoft.com/pt-br/azure/architecture/example-scenario/unix-migration/media/aix-on-premises-system.png)](https://docs.microsoft.com/pt-br/azure/architecture/example-scenario/unix-migration/media/aix-on-premises-system.png#lightbox)

*Baixe um [Arquivo Visio](https://arch-center.azureedge.net/UNIX-AIX-Azure-RHEL-migration.vsdx) dessa arquitetura.*

- Os dispositivos de rede fornecem um amplo roteamento de rede e camada de balanceamento de carga**(A).**
- A camada de apresentação (**B**) usa três máquinas de front-end da Web Java em sua própria sub-rede, que segmenta o tráfego de rede por firewalls.
- Firewalls (**C**) fornecem limites de rede entre todas as camadas e subsistemas participantes. Embora os firewalls sejam eficazes, eles também são uma carga administrativa.
- O sistema fornece solicitações de usuário para a camada de aplicativo (**D**), que tem três servidores de aplicativo Web.
- A camada de aplicativo chama o banco de dados DB2 e o NAS (armazenamento anexado à rede):
  - O banco de dados (**E**) é DB2 no AIX. Três servidores DB2 são configurados em um cluster de HA/DR.
  - O aplicativo armazena objetos binários como imagens e PDFs para clientes e usuários em um subsistema NAS (**F**).
- Os servidores de gerenciamento e administração e os servidores MQ (**G**) estão em sua própria sub-rede, segmentados por firewalls.
- Os serviços de gerenciamento de identidade do LDAP (Lightweight Directory Access Protocol) (**H**) estão em sua própria sub-rede, segmentadas por firewalls.

O diagrama a seguir mostra a arquitetura do sistema de pós-migração do RHEL do Azure:

[![Diagrama que mostra a arquitetura do Azure pós-migração.](https://docs.microsoft.com/pt-br/azure/architecture/example-scenario/unix-migration/media/rhel-azure-system.png)](https://docs.microsoft.com/pt-br/azure/architecture/example-scenario/unix-migration/media/rhel-azure-system.png#lightbox)

*Baixe um [Arquivo Visio](https://arch-center.azureedge.net/UNIX-AIX-Azure-RHEL-migration.vsdx) dessa arquitetura.*

1. O tráfego para o sistema do Azure é Azure ExpressRoute e Gerenciador de Tráfego do Azure:
   - O ExpressRoute fornece uma conexão privada segura e confiável para redes virtuais do Azure. O ExpressRoute se conecta ao Azure com baixa latência, alta confiabilidade e velocidade e larguras de banda de até 100 Gbps.
   - Gerenciador de Tráfego distribui o tráfego de aplicativo voltado para o público entre regiões do Azure.
2. Uma camada de gerenciamento de rede fornece serviços de segurança, roteamento e balanceamento de carga do ponto de extremidade. Essa camada usa Azure Load Balancer e Firewall de Aplicativo Web do Azure.
3. Serviço de Aplicativo do Azure serve como a camada de apresentação. O Serviço de Aplicativo é uma camada de PaaS (plataforma como serviço) para aplicativos .NET ou Java. Você pode configurar o Serviço de Aplicativo para disponibilidade e escalabilidade dentro e entre regiões do Azure.
4. A solução encapsula cada camada de aplicativo em sua própria rede virtual, segmentada com grupos de segurança de rede.
5. [Conjuntos de disponibilidade](https://docs.microsoft.com/pt-br/azure/virtual-machines/availability-set-overview) e aplicativos compartilhados do Azure Armazenamento ha e escalabilidade para VMs (máquinas virtuais) no nível da camada de aplicativo. Os servidores de cluster de aplicativos compartilham o estado da transação e dimensionam as VMs conforme necessário.
6. O aplicativo usa uma [conexão de ponto de extremidade](https://docs.microsoft.com/pt-br/azure/private-link/tutorial-private-endpoint-sql-portal) privado para armazenar e acessar dados Banco de Dados SQL do Azure. Banco de Dados SQL é executado em uma configuração de continuidade de negócios, que fornece replicação geográfica e grupos de autofailover para BCDR automática e entre geográficos.
7. Azure NetApp Files fornece um NAS compartilhado, com acesso rápido a dados binários e replicação para a região secundária.
8. A região secundária fornece BCDR com os seguintes componentes:
   - Azure Site Recovery faz o back-up de imagens de VM para failover de DR em uma configuração ativo-passivo. Site Recovery cria réplicas de imagem de VM consistentes na região secundária e mantém as imagens de VM em sincronia.
   - Banco de Dados SQL configuração de continuidade dos negócios mantém as transações de banco de dados consistentes. Banco de Dados SQL provisiona bancos de dados de réplica e os mantém em sincronia com a replicação de dados síncrona ou assíncrona.

O sistema também contém os seguintes componentes:

- Uma ou mais VMs na rede virtual de gerenciamento fornecem funcionalidade de gerenciamento e administração.
- O Azure Barramento de Serviço a infraestrutura da Série MQ e fornece serviços de fila de mensagens para os aplicativos. Para obter mais informações sobre como migrar da série MQ para o Azure Barramento de Serviço, consulte Migrar do ActiveMQ para o [Azure Barramento de Serviço](https://docs.microsoft.com/pt-br/azure/service-bus-messaging/migrate-jms-activemq-to-servicebus).
- Azure Active Directory (Azure AD) fornece gerenciamento de identidade e acesso para todas as entidades e identidades do Azure migradas dos serviços LDAP herdados.

### Componentes

- [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute) estende uma rede local para os serviços de nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. O ExpressRoute fornece uma conexão privada segura e confiável com o sistema do Azure, com baixa latência e alta velocidade e largura de banda.
- [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager) é um balanceador de carga de tráfego baseado em DNS que distribui o tráfego entre regiões do Azure, com alta disponibilidade e capacidade de resposta rápida.
- [Azure Load Balancer](https://azure.microsoft.com/services/load-balancer) dá suporte à alta disponibilidade distribuindo o tráfego de rede de entrada entre VMs de back-end de acordo com as regras de balanceamento de carga configuradas e as investigações de saúde. Load Balancer opera na camada 4 do modelo OSI (Open Systems Interconnection).
- [Firewall de Aplicativo Web do Azure](https://azure.microsoft.com/services/web-application-firewall) é um serviço WAF nativo de nuvem que protege aplicativos Web contra ataques mal-intencionados e vulnerabilidades comuns da Web.
- [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service) é um serviço de hospedagem na Web totalmente gerenciado para implantar aplicativos Web empresariais de forma rápida e fácil para qualquer plataforma em uma infraestrutura de nuvem escalonável e confiável.
- [As Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines) são um dos vários serviços do Azure que fornecem recursos de computação escalonáveis sob demanda. Com as VMs do Azure, você tem a flexibilidade da virtualização sem precisar comprar e manter o hardware físico.
  - [Os discos gerenciados do SSD do Azure](https://docs.microsoft.com/pt-br/azure/virtual-machines/windows/managed-disks-overview) são volumes de armazenamento em nível de bloco para VMs do Azure.
  - [As NICs (placas de interface](https://docs.microsoft.com/pt-br/azure/virtual-network/virtual-network-network-interface) de rede virtual) do Azure permitem que as VMs do Azure se comuniquem com a Internet, o Azure e os recursos locais. Você pode adicionar várias NICs virtuais a uma VM do Azure, para que as VMs filho possam ter seus próprios dispositivos de interface de rede dedicados e endereços IP.
- [A Rede Virtual do Azure](https://azure.microsoft.com/services/virtual-network) é o bloco de construção fundamental para redes privadas do Azure. A Rede Virtual permite que muitos tipos de recursos do Azure, como VMs, se comuniquem com segurança entre si, com a Internet e com redes locais. A Rede Virtual oferece benefícios de infraestrutura do Azure, como escalabilidade, disponibilidade e isolamento.
- [Arquivos do Azure](https://azure.microsoft.com/services/storage/files) armazenamento oferece compartilhamentos de arquivos totalmente gerenciados na nuvem que podem ser acessados por meio do protocolo SMB (Server Message Block) padrão do setor. Implantações de nuvem e locais Windows, Linux e macOS podem montar compartilhamentos de arquivos do Azure simultaneamente.
- [Banco de Dados SQL do Azure](https://azure.microsoft.com/products/azure-sql/database) é um PaaS de banco de dados totalmente gerenciado que sempre é executado no sistema operacional mais recente e estável SQL Server do mecanismo de banco de dados, com disponibilidade mais alta. Banco de Dados SQL gerencia funções de gerenciamento de banco de dados, como atualizações, a patch, backups e monitoramento, sem envolvimento do usuário.
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp) oferece compartilhamentos de arquivos do Azure de nível empresarial da NetApp. Azure NetApp Files facilita para as empresas migrar e executar aplicativos complexos baseados em arquivo sem alterações de código.
- [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery) é um serviço de DR nativo do Azure. Site Recovery implanta processos de replicação, failover e recuperação para ajudar a manter os aplicativos em execução durante as inatividades planejadas e não planejadas.
- [O Azure Barramento de Serviço](https://azure.microsoft.com/services/service-bus) é um serviço de mensagens de nuvem confiável com integração híbrida simples.
- [O Azure AD é](https://azure.microsoft.com/services/active-directory) o serviço de gerenciamento de acesso e identidade corporativa baseado em nuvem da Microsoft. O acesso único e a autenticação multifator do Azure AD ajudam os usuários a entrar e acessar recursos, enquanto protegem contra ataques de segurança cibernética.

### Alternativas

[Serviço de Aplicativo do Azure ambientes são apropriados](https://docs.microsoft.com/pt-br/azure/app-service/environment/intro) para cargas de trabalho de aplicativo que exigem alta escala, isolamento e acesso seguro à rede. Esse recurso oferece ambientes totalmente isolados e dedicados para executar com segurança aplicativos do Serviço de Aplicativo em grande escala. Os ambientes do Serviço de Aplicativo podem hospedar os seguintes tipos de aplicativos:

- Aplicativos Web do Linux, como no exemplo atual
- Aplicativos Web do Windows
- Contêineres do Docker
- Aplicativos móveis
- Funções

## Considerações

As seguintes considerações, com base [no Microsoft Azure Well-Architected Framework](https://docs.microsoft.com/pt-br/azure/architecture/framework), se aplicam a esta solução:

### Disponibilidade

- Azure NetApp Files pode manter o armazenamento de arquivos na região secundária atualizado com a replicação entre regiões [de Azure NetApp Files Volumes](https://docs.microsoft.com/pt-br/azure/azure-netapp-files/cross-region-replication-introduction). Esse recurso do Azure fornece proteção de dados por meio da replicação de volume entre regiões. Você poderá fazer fail over de aplicativos críticos se houver uma falha em toda a região. A replicação de volume entre regiões está atualmente em versão prévia.
- Os servidores de cluster de aplicativos escalam as VMs conforme necessário, o que aumenta a disponibilidade nas regiões do Azure.

### Operations

Para monitoramento e gerenciamento proativos, considere [usar](https://azure.microsoft.com/services/monitor) Azure Monitor para monitorar cargas de trabalho AIX migradas.

### Desempenho

- Os possíveis gargalos nessa arquitetura são os subsistemas de armazenamento e computação. Certifique-se de escolher seu armazenamento e SKUs de VM de acordo.
- Os tipos de disco de VM disponíveis são discos ultra, SSDs (unidades de estado sólido) Premium, SSDs standard e HDDs (unidades de disco rígido padrão). Para essa solução, é melhor usar SSDs Premium ou discos ultra.
- Para estimar o cálculo de VMs provenientes de um sistema AIX, tenha em mente que as CPUs AIX são cerca de 1,4 vezes mais rápidas do que a maioria das x86 vCPUs. Essa diretriz pode variar de acordo com a carga de trabalho.
- Coloque várias VMs que precisam se comunicar entre si em um grupo [de posicionamento por proximidade.](https://docs.microsoft.com/pt-br/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) Localizar as VMs próximas umas das outras fornece a menor latência de comunicação.

### Escalabilidade

- [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute) dá suporte a alta escala para implementações que usam largura de banda significativa, seja para replicação inicial ou replicação de dados alterada em andamento.
- O gerenciamento de infraestrutura, incluindo escalabilidade, é automatizado em bancos de dados do Azure.
- Você pode escalar a camada de aplicativo de forma escalonada adicionando mais instâncias de VM do servidor de aplicativos.

### Segurança

- Essa solução usa grupos de segurança de rede do Azure para gerenciar o tráfego entre os recursos do Azure. Para saber mais, confira [Grupos de segurança de rede](https://docs.microsoft.com/pt-br/azure/virtual-network/network-security-groups-overview).
- Siga [as melhores práticas do Azure para segurança de](https://docs.microsoft.com/pt-br/azure/security/fundamentals/network-best-practices) rede o mais próximo possível.
- Para segurança de IaaS (infraestrutura como serviço) ou VM, siga as práticas recomendadas de segurança para cargas de trabalho [de IaaS no Azure.](https://docs.microsoft.com/pt-br/azure/security/fundamentals/iaas)

## Preços

- A migração de cargas de trabalho do AIX para o Linux no Azure pode trazer uma economia significativa de custos. Você elimina a manutenção de hardware, reduz os custos das instalações e geralmente pode reduzir os custos operacionais em um fator de oito a 10. O Azure pode acomodar capacidade adicional para cargas de trabalho sazonais ou periódicas conforme necessário, o que reduz o custo geral.
- A migração de cargas de trabalho do AIX para o Azure também pode reduzir os custos usando serviços nativos de nuvem. Os exemplos incluem:
  - Usando Serviço de Aplicativo do Azure para a camada de apresentação em vez de configurar várias VMs.
  - Segmentar cargas de trabalho com redes virtuais do Azure em vez de usar firewalls baseados em hardware.

## Próximas etapas

- [Migrando cargas de trabalho do AIX para o Azure: abordagens e práticas recomendadas.](https://techcommunity.microsoft.com/t5/azure-global/migrating-aix-workloads-to-azure-approaches-and-best-practices/ba-p/1085983)
- Guia de Planejamento de Migração Estratégica do [AIX para Red Hat Enterprise Linux](https://www.intel.com/content/dam/www/public/us/en/documents/guides/risc-migration-aix-to-red-had-enterprise-linux-strategic-migration-planning-guide.pdf).
- Para obter mais informações, entre em contato [legacy2azure@microsoft.com](mailto:legacy2azure@microsoft.com) com .

## Recursos relacionados

- [Cenários de alta disponibilidade e recuperação de desastre para aplicativos IaaS](https://docs.microsoft.com/pt-br/azure/architecture/example-scenario/infrastructure/iaas-high-availability-disaster-recovery)
- [Aplicativo Web de várias camadas criado para HA/DR](https://docs.microsoft.com/pt-br/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)
- [Aplicativo de n camadas de várias regiões](https://docs.microsoft.com/pt-br/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
- [Executar uma VM do Linux no Azure](https://docs.microsoft.com/pt-br/azure/architecture/reference-architectures/n-tier/linux-vm)

------

## Conteúdo recomendado

- [Arquiteturas para implantar aplicativos Oracle em Máquinas Virtuais do Azure - Azure Virtual Machines](https://docs.microsoft.com/pt-br/azure/virtual-machines/workloads/oracle/oracle-oci-applications)

  Arquiteturas de aplicativo para implantar aplicativos Oracle, incluindo o E-Business Suite, o JD Edwards EnterpriseOne e o PeopleSoft, em máquinas virtuais do Microsoft Azure com bancos de dados no Azure ou na OCI (Oracle Cloud Infrastructure).

- [Migração do Oracle Database: Lift-and-shift - Azure Example Scenarios](https://docs.microsoft.com/pt-br/azure/architecture/example-scenario/oracle-migrate/oracle-migration-lift-shift)

  Faça lift-and-shift do seu Oracle Database de um ambiente Oracle para Máquinas Virtuais do Azure.

- [Recuperação de desastre de escala empresarial - Azure Solution Ideas](https://docs.microsoft.com/pt-br/azure/architecture/solution-ideas/articles/disaster-recovery-enterprise-scale-dr)

  essa arquitetura de grande empresa para os servidores web SharePoint, Dynamics CRM e Linux é executada em um datacenter local e faz failover para a infraestrutura do Azure.

- [Rede de soluções do Azure VMware - Azure Solution Ideas](https://docs.microsoft.com/pt-br/azure/architecture/solution-ideas/articles/azure-vmware-solution-foundation-networking)

  Introdução ao Azure VMware Solution Foundation bloquear rede. Entenda quais opções de solução de rede existem e como escolher entre elas.