# 1- Introdução 💻
A empresa Brasil317 é uma empresa de licitações com sistema ainda em construção. Sendo assim ela ainda não possui um sistema de suportes, tendo como consequência que todas as alterações dos pregões precisam ser feitas diretamente no banco de dados, aumentando a possibilidade de erros e um enorme prejuízo financeiro a empresa.
## 1.1- Definição do problema 😵‍💫
Conforme o problema citado na introdução, foi proposto o desafio de criação de um sistema de suporte para diminuir as alterações feitas diretamente no Banco de Dados. Dessa forma, somente o líder do setor de suportes terá a permissão de fazer alterações diretamente no Banco de Dados. Para os demais integrantes da empresa, como operações e estagiários, somente será permitida a alteração através do sistema.
## 1.2- Objetivo 🎯
O objetivo deste trabalho consiste em construir uma API REST e posteriormente um Fron end para executar suportes diminuindo assim a necessidade de alterações diretamente no Banco de Dados.
# 2- Desenvolvimento 🖋️
Para o desenvolvimento desse trabalho está sendo utilizado ASP NET CORE 3.1, API REST e programação Front end por VUE. O banco de dados é Postgresql.
## 2.1- Arquitetura 🏛️
A arquitetura REST possui uma série de regras e princípios que devem ser seguidas:
* **Cliente-Servidor:** Trata de separar as preocupações de interface do usuário (User Interface) do banco de dados, abstraindo a dependência entre os lados clientes/servidor e permitindo a evolução desses componentes sem impacto e quebra de contrato.
* **Interface Uniforme:** É a interoperabilidade entre os componentes cliente e servidor. Como o cliente e servidor compartilham da mesma interface, é necessário estabelecer um contrato para a comunicação entre essas partes.
* **Stateless:** Cada requisição acionada entre a comunicação cliente-servidor deve possuir toda a informação necessária e compreensível para realizar a origem da requisição, não sendo de responsabilidade do servidor armazenar qualquer tipo de contexto. Isso pode gerar alto tráfego de dados e impacto na performance da aplicação, porém pode-se utilizar recursos de cache nesses casos.
* **Cache:** É utilizado para melhorar a performance de comunicação entre aplicações, otimizando o tempo de resposta na comunicação entre cliente-servidor.
* **Camadas:** Nesse modelo, o cliente não deve conectar-se diretamente ao servidor da aplicação, porém uma camada de balanceamento de carga deverá ser acionada para essa responsabilidade.
<br>

<div align="center">
<img src="https://user-images.githubusercontent.com/51200164/171039474-1a83ebe1-d8df-4288-b37c-f886d648c597.png" width="700px" />
</div>

## 2.2- Detalhes 🧑🏻‍💻
* **Declaração da Rota e variáveis a serem inseridas no Postman:**
```c#
using System;
using System.Globalization;
using System.Linq;
using System.Threading.Tasks;
using brasil317_support.Models;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace brasil317_support.Controllers
{

[Route("auctioncontroller")] // Declaração da rota principal
public class AuctionController : ControllerBase
    {
        bool portalValidation = false;
        
        [HttpPut]
        [Route("changedates")] // Declaração da rota secundária
        public async Task<ActionResult<TblOpportunity>> PutChangeAuctionDates([FromServices] b317_systemContext context)
        {
            // Declaração das variáveis que serão colocadas no Postman
            var auctionNumber = Request.Query["auction_number"].ToString();
            var buyerUnit = Request.Query["buyer_unit"].ToString();
            var account = Request.Query["account"].ToString();
            var portal = Request.Query["portal"].ToString();
            var targetPorposal = Request.Query["target_proposal"].ToString();
            var auctionDate = Request.Query["auction_date"].ToString();
            var publishDate = Request.Query["publish_date"].ToString();
            var initialTargetProposal = Request.Query["initial_target_proposal"].ToString();
```
<br>

* **Envia para a classe FillDateValidation(auctionNumber,portal,account) para verificar se o auctionNumber, portal e account estão preenchidos:**
```c#
// Verifica se o auctionNumber, portal e account estão preenchidos
bool inputDataValidation = Util.Validation.FillDateValidation(auctionNumber,portal,account);

            if (inputDataValidation == false) // Se retornou falso é porque o auction_number, buyer_unit ou account estão vazios
                return BadRequest(new { message = "É obrigatório o preenchimento dos campos auction_number, buyer_unit, account e portal (O campo buyer_unit não é obrigatório no caso do porta BB)" });
```
<br>

* **Classe FillDateValidation:**
```c#
// Classe que faz a verificação se o auctionNumber, account ou portal estão preenchidos
public static bool FillDateValidation (string auctionNumber, string account, string portal)
        {
            if (string.IsNullOrEmpty(auctionNumber) || string.IsNullOrEmpty(account) || string.IsNullOrEmpty(portal))
                return false;
            
            return true;
        }
```
<br>

* **Envia para a classe ValidatePortal(portal) para verificar se o portal é válido.**
```c#
// Envia para a classe de validação do portal
portalValidation = Util.Validation.ValidatePortal(portal);
            
            // Se o retornar falso, não é um portal válido
            if (portalValidation == false)
                return BadRequest(new { message = "Portal não encontrado" });
```
<br>

* **Classe ValidatePortal:**
```c#
// Verifica se o portal é BB, BEC ou CN
public static bool ValidatePortal(string portal)
        {
            if (portal != "BB" && portal != "BEC" && portal != "CN")
                return false;
            
            return true;
        }
```
<br>

* **Envia para a classe DateValidationC para verificação se a data é Válida:**
```c#
            Declaração das variáveis para para as datas Target Proposal, Auction Date, Publish Date e Initial Targuet Proposal
            bool validateTp = true;
            bool validateAd = true;
            bool validatePd = true;
            bool validadeItp = true;
            
            // Faz o envio para a classe DateValidationC que é um regex que valida as datas 
            if (!string.IsNullOrEmpty(targetPorposal))
                validateTp = Util.Validation.DateValidationC(targetPorposal);
            if (!string.IsNullOrEmpty(auctionDate))
                validateAd = Util.Validation.DateValidationC(auctionDate);
            if (!string.IsNullOrEmpty(publishDate))
                validatePd = Util.Validation.DateValidationC(publishDate);
            if (!string.IsNullOrEmpty(initialTargetProposal))
                validadeItp = Util.Validation.DateValidationC(initialTargetProposal);
            
            // Se alguma data retornar false ou seja inválida dá um BadRequest
            if (validateTp == false || validateAd == false || validatePd == false || validadeItp == false)
                return BadRequest(new { message = "Os campos target_proposal e auction_date deve estar no formato dd/MM/yyyy HH:mm:ss ou as datas são inválidas" });
```
<br>

* **Classe DateValidationC: (Feita com regex)**
```c#
// Classe regex que faz a validação das datas
public static bool DateValidationC(string data) // Validação completa para data e hora (\d = [0-9])
        {
            Regex r = new Regex(@"(((0[1-9]|[12]\d|3[01])\/(0[13578]|1[02])\/(20[1-9]{2}) ([01]\d|2[0-3]):([0-5]\d):([0-5]\d))|((0[1-9]|[12]\d|3[0])\/(0[469]|1[1])\/(20[1-9]{2}) ([01]\d|2[0-3]):([0-5]\d):([0-5]\d))|((0[1-9]|1\d|2[0-8])\/02\/(20[1-9]{2}) ([01]\d|2[0-3]):([0-5]\d):([0-5]\d))|(29\/02\/((1[6-9]|[2-9]\d)(0[48]|[2468][048]|[13579][26])|((16|[2468][048]|[3579][26])00))))");
            return r.Match(data).Success;
        }
```
<br>

* **Se o pregão for do BB, pega o status do pregão solicitado, verifica o status do pregão e pega as datas do pregão:**
```c#
// Se o pregão for do BB
if (portal == "BB")
            {
                // Pega na tabela TblAuctionFlow todos os dados do pregão pelo AuctionNumber, Account e Portal fornecidos no Postman
                var aflow = await context.TblAuctionFlow.Where(x => x.AuctionNumber == auctionNumber && x.Account == account && x.Portal == portal).FirstOrDefaultAsync();
                
                // Pega o status do pregão solicitado
                var auctionStatus = aflow.StatusName.ToString();
                
                // Caso o status do pregão seja OPEN, REFUSED, PENDING_REGISTER, PENDING_RESULT ou HOMOLOGATE, retorna um BadRequest
                if (auctionStatus == "OPEN" || auctionStatus == "REFUSED" || auctionStatus == "PENDING_REGISTER" || auctionStatus == "PENDING_RESULT" || auctionStatus == "HOMOLOGATED")
                    return BadRequest(new { message = "As datas não podem ser alteradas para o pregão com status " + auctionStatus });
                
                // Pega na tabela TblOpportunity todos os dados do pregão pelo AuctionNumber, Account e Portal fornecidos no Postman
                var opportunity = await context.TblOpportunity.Where(x => x.AuctionNumber == auctionNumber && x.Account == account && x.Portal == portal).FirstOrDefaultAsync();

                var dateTp1 = opportunity.TargetProposal.ToString("dd/MM/yyyy HH:mm:ss"); // pega a TargetProposal do pregão que está registrada no BD
                var dateAd1 = opportunity.AuctionDate.ToString("dd/MM/yyyy HH:mm:ss"); // pega a AuctionDate do pregão que está registrada no BD
                var datePd1 = opportunity.PublishDate.ToString("dd/MM/yyyy HH:mm:ss"); // pega o PublishDate do pregão que está registrada no BD
                var dateItp1 = opportunity.InitialTargetProposal.ToString("dd/MM/yyyy HH:mm:ss"); // pega o InitialTargetProposal que esta registrada no BD

                CultureInfo Culture = new CultureInfo("pt-BR");
```
<br>

* **Faz a comparação das datas do pregão que estão no banco com as novas datas. Se as novas datas forem iguais do banco ou nulas, passa para a próxima data:**
```c#
                // Verifica se a data dateTp1 retornada da tabela é diferente da data digitada no Postman ou se a data auction_date do Postman está vazia
                if (dateTp1 != targetPorposal && !string.IsNullOrEmpty(targetPorposal))
                {
                    var data = Convert.ToDateTime(targetPorposal, Culture).AddHours(3);
                    opportunity.TargetProposal = data;
                    opportunity.TargetOperational = data;
                    opportunity.TargetClient = data;

                    resultadoTp = "targuet_proposal";
                }
                
                // Verifica se a data dateAd1 retornada da tabela é diferente da data digitada no Postman ou se a data target_proposal do Postman está vazia
                if (dateAd1 != auctionDate && !string.IsNullOrEmpty(auctionDate))
                {
                    var data1 = Convert.ToDateTime(auctionDate, Culture).AddHours(3);
                    opportunity.AuctionDate = data1;
                    resultadoAd = "auction_date";
                }
                
                // Verifica se a data datePd1 retornada da tabela é diferente da data digitada no Postman ou se a data publish_date do Postman está vazia
                if (datePd1 != publishDate && !string.IsNullOrEmpty(publishDate))
                {
                    var data2 = Convert.ToDateTime(publishDate, Culture).AddHours(3);
                    opportunity.PublishDate = data2;
                    resultadoPd = "publish_date";
                }

                // Verifica se a data dateItp1 retornada da tabela é diferente da data digitada no Postman ou se a data initial_target_proposal do Postman está vazia
                if (dateItp1 != initialTargetProposal && !string.IsNullOrEmpty(initialTargetProposal))
                {
                    var data3 = Convert.ToDateTime(initialTargetProposal, Culture).AddHours(3);
                    opportunity.InitialTargetProposal = data3;
                    resultadoItp = "initial_target_proposal";
                }
```
<br>

* **Se o pregão for do CN ou BEC, verifica se o buyerUnit está preenchido, pega o status do pregão solicitado, verifica o status do pregão e pega as datas do pregão:**
```c#
            // Se o pregão for do CN ou BEC e se o buyerUnit não for vazio ou nulo
            if ((portal == "CN" || portal == "BEC") && !string.IsNullOrEmpty(buyerUnit))
            {
                // Pega na tabela TblAuctionFlow todos os dados do pregão pelo AuctionNumber, BuyerUnit, Account e Portal fornecidos no Postman
                var aflow = await context.TblAuctionFlow.Where(x => x.AuctionNumber == auctionNumber && x.BuyerUnit == buyerUnit && x.Account == account && x.Portal == portal).FirstOrDefaultAsync();
                
                // Pega o status do pregão solicitado
                var auctionStatus = aflow.StatusName.ToString();
                
                // Caso o status do pregão seja OPEN, REFUSED, PENDING_REGISTER, PENDING_RESULT ou HOMOLOGATE, retorna um BadRequest
                if (auctionStatus == "OPEN" || auctionStatus == "REFUSED" || auctionStatus == "PENDING_REGISTER" || auctionStatus == "PENDING_RESULT" || auctionStatus == "HOMOLOGATED")
                    return BadRequest(new { message = "As datas não podem ser alteradas para o pregão com status " + auctionStatus });
                
                // Pega na tabela TblOpportunity todos os dados do pregão pelo AuctionNumber, Account e Portal fornecidos no Postman
                var opportunity = await context.TblOpportunity.Where(x => x.AuctionNumber == auctionNumber && x.BuyerUnit == buyerUnit && x.Account == account && x.Portal == portal).FirstOrDefaultAsync();

                var dateTp1 = opportunity.TargetProposal.ToString("dd/MM/yyyy HH:mm:ss"); // pega a TargetProposal do pregão que está registrada no BD
                var dateAd1 = opportunity.AuctionDate.ToString("dd/MM/yyyy HH:mm:ss"); // pega a AuctionDate do pregão que está registrada no BD
                var datePd1 = opportunity.PublishDate.ToString("dd/MM/yyyy HH:mm:ss"); // pega o PublishDate do pregão que está registrada no BD
                var dateItp1 = opportunity.InitialTargetProposal.ToString("dd/MM/yyyy HH:mm:ss"); // pega o InitialTargetProposal que esta registrada no BD

                CultureInfo Culture = new CultureInfo("pt-BR");
```
<br>

* **Faz a comparação das datas do pregão que estão no banco com as novas datas. Se as novas datas forem iguais do banco ou nulas, passa para a próxima data:**
```c#
                // Verifica se a data dateTp1 retornada da tabela é diferente da data digitada no Postman ou se a data auction_date do Postman está vazia
                if (dateTp1 != targetPorposal && !string.IsNullOrEmpty(targetPorposal))
                {
                    var data = Convert.ToDateTime(targetPorposal, Culture).AddHours(3);
                    opportunity.TargetProposal = data;
                    opportunity.TargetOperational = data;
                    opportunity.TargetClient = data;

                    resultadoTp = "targuet_proposal";
                }
                
                // Verifica se a data dateAd1 retornada da tabela é diferente da data digitada no Postman ou se a data target_proposal do Postman está vazia
                if (dateAd1 != auctionDate && !string.IsNullOrEmpty(auctionDate))
                {
                    var data1 = Convert.ToDateTime(auctionDate, Culture).AddHours(3);
                    opportunity.AuctionDate = data1;
                    resultadoAd = "auction_date";
                }
                
                // Verifica se a data datePd1 retornada da tabela é diferente da data digitada no Postman ou se a data publish_date do Postman está vazia
                if (datePd1 != publishDate && !string.IsNullOrEmpty(publishDate))
                {
                    var data2 = Convert.ToDateTime(publishDate, Culture).AddHours(3);
                    opportunity.PublishDate = data2;
                    resultadoPd = "publish_date";
                }
                
                // Verifica se a data dateItp1 retornada da tabela é diferente da data digitada no Postman ou se a data initial_target_proposal do Postman
                if (dateItp1 != initialTargetProposal && !string.IsNullOrEmpty(initialTargetProposal))
                {
                    var data3 = Convert.ToDateTime(initialTargetProposal, Culture).AddHours(3);
                    opportunity.InitialTargetProposal = data3;
                    resultadoItp = "initial_target_proposal";
                }
```
<br>

* **Caso o portal seja do CN ou BEC e o buyerUnit não esteja preenchido:**
```c#
            // Caso o portal seja do CN ou BEC e o buyerUnit for vazio ou Nulo retorna um BadRequest
            if ((portal == "CN" || portal == "BEC") && string.IsNullOrEmpty(buyerUnit))
                return BadRequest(new { message = "É obrigatório o preenchimento do campo buyer_unit para os portais CN ou BEC" });
        
            // Salva as mudanças no banco e mostra quais mudanças ocorreram
            context.SaveChanges();
            return Ok(new { message = "Datas alteradas: " + resultadoTp + " - " + resultadoAd +  " - " + resultadoPd +  " - " + resultadoItp });
            }
            catch
            {
                // Caso retorne um erro é porque o pregão não foi encontrado
                return BadRequest(new { message = "Pregão não encontrado" });
            }
```
<br>

* **Tela do Postman:**
<br>

<div align="center">
<img src="https://user-images.githubusercontent.com/51200164/171040109-dd12ff73-f93e-40db-bed1-4988d0865b1b.png" width="700px" />
</div>
