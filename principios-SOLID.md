# 📘 Guia Rápido: Princípios SOLID (Backend Java / Mundo Real)

## S - Single Responsibility Principle (Responsabilidade Única)
**O que é:** Uma classe deve ter apenas um motivo para mudar. Separe persistência no banco, regra de negócio e infraestrutura.

**❌ Errado:** Uma classe `PedidoService` que valida o estoque, salva o pedido no banco com SQL na mão e ainda dispara o e-mail de confirmação.

**✅ Certo:**
```java
public class PedidoService {
    private final PedidoRepository repository; // Só cuida do banco
    private final EmailService emailService;   // Só cuida de notificar

    public void finalizarPedido(Pedido pedido) {
        // ... validações isoladas ...
        repository.save(pedido);
        emailService.enviarConfirmacao(pedido.getCliente().getEmail());
    } 
}
```

## O - Open/Closed Principle (Aberto/Fechado)
**O que é:** O código deve estar fechado para alteração, mas aberto para extensão. Fuja de `if/else` gigantes quando novas regras surgirem.

**❌ Errado:** Um método de pagamento com `if (tipo == PIX) {...} else if (tipo == CARTAO) {...}`. Se a loja aceitar Criptomoeda amanhã, você tem que alterar a classe e arriscar quebrar as outras.

**✅ Certo (Usando Polimorfismo / Padrão Strategy):**
```java
public interface ProcessadorPagamento {
    void processar(BigDecimal valor);
}

// Aceitou um pagamento novo? Basta criar uma CLASSE nova. A antiga fica intacta.
public class PagamentoPix implements ProcessadorPagamento {
    public void processar(BigDecimal valor) { /* Integra com API do Bacen */ }
}

public class PagamentoCartao implements ProcessadorPagamento {
    public void processar(BigDecimal valor) { /* Integra com a Cielo/Stone */ }
}
```

## L - Liskov Substitution Principle (Substituição de Liskov)
**O que é:** Se você tem uma lista de uma Interface/Classe Pai, qualquer implementação filha deve funcionar sem quebrar o sistema (sem lançar exceções inesperadas).

**❌ Errado:** Interface `Produto` com método `calcularFrete()`. A classe `LivroFisico` calcula normal, mas a classe `EbookDigital` lança um `UnsupportedOperationException` porque não tem frete. Se um `for` rodar a lista no carrinho, o sistema quebra na hora do checkout.

**✅ Certo:**
```java
public interface Produto {
    BigDecimal getPreco();
}

// Só implementa essa interface quem REALMENTE precisa de transporte
public interface Transportavel {
    BigDecimal calcularFrete(String cep); 
}

public class LivroFisico implements Produto, Transportavel { ... }
public class EbookDigital implements Produto { ... } // Não tem frete, sistema seguro!
```

## I - Interface Segregation Principle (Segregação de Interfaces)
**O que é:** Não crie interfaces "gordas" genéricas. É melhor ter várias interfaces específicas do que obrigar uma classe a implementar um método que ela não usa.

**❌ Errado:** Uma interface `GatewayPagamento` com `pagarPix()`, `pagarCredito()` e `gerarBoleto()`. Se você plugar um gateway moderno que *só aceita Pix*, os outros métodos ficarão vazios ou retornando erro.

**✅ Certo:**
```java
public interface PagamentoPixAPI {
    void pagarPix(DadosPix dados);
}
public interface PagamentoBoletoAPI {
    void gerarBoleto(DadosBoleto dados);
}
// O Gateway A faz os dois, implementa as duas. O Gateway B faz só Pix, implementa só a primeira.
```

## D - Dependency Inversion Principle (Inversão de Dependência)
**O que é:** Seu código de alto nível deve depender de Interfaces (abstrações) e não de classes concretas ou bibliotecas específicas.

**❌ Errado:** O seu PedidoService depende diretamente de uma classe concreta chamada CorreiosAPI. Se os Correios entrarem em greve amanhã e a loja quiser usar a transportadora Jadlog, você vai ter que abrir a classe PedidoService e reescrever a regra de negócio.

**✅ Certo:**
```java
// A interface (O Contrato)
public interface CalculadorDeFrete {
    BigDecimal calcular(String cep);
}

@Service
public class PedidoService {
    
    // O Service depende de uma ABSTRAÇÃO, não importa quem vai entregar.
    private final CalculadorDeFrete calculadorDeFrete; 

    // O Spring (ou CDI) injeta a implementação que estiver configurada,
    // seja Correios, Jadlog ou FedEx. A regra do Pedido fica intacta!
    public PedidoService(CalculadorDeFrete calculadorDeFrete) {
        this.calculadorDeFrete = calculadorDeFrete;
    }
}
```
