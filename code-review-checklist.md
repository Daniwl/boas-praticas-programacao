# Code Review Checklist - Backend Java

## Arquitetura
- [ ] O Controller está limpo (sem `if/else` ou regras de negócio)?
- [ ] A regra de negócio está 100% isolada na camada de Service?
- [ ] O Repository faz estritamente o acesso a dados?

## Tráfego e Segurança
- [ ] A API recebe e retorna DTOs em vez de Entidades (`@Entity`) do banco?
- [ ] Dados sensíveis (senhas, IDs internos) foram removidos da resposta?

## Padrão REST e Respostas
- [ ] As rotas estão no plural (ex: `/clientes`)?
- [ ] Os Status HTTP estão corretos (200, 201, 204, 400, 404)?
- [ ] O tratamento de erros é global (sem vazar Erro 500 ou stacktrace na tela)?

## Validação e Clean Code
- [ ] O Controller usa `@Valid` e o DTO possui anotações de restrição (`@NotNull`, etc.)?
- [ ] Variáveis e métodos possuem nomes claros (sem `x`, `aux`, `data`)?
- [ ] Valores fixos foram extraídos para Constantes ou Enums (zero "magic numbers")?
