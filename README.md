# TDD e Java: Testes automatizados com JUnit

### **@Teste → Notation do JUnit**

Usado em cima do método que quer testar, em vez de rodar em algum método main, basta colocar a notation, e Run as> JUnit test

### Assert.assertEquals(valorEsperado, variavelVerificar) → Classe do Junit

Método para verificar se duas variáveis são iguais, dois valores são equivalentes, Então ele recebe dois parâmetros, o primeiro, qual o valor que estou esperando, e o segundo, quem é o valor, quem é a variável que ele vai verificar.

Instalação do JUnit via pom.xml :

```xml
<dependency>
			<groupId>org.junit.jupiter</groupId>
			<artifactId>junit-jupiter-engine</artifactId>
			<version>5.7.0</version>
			<scope>test</scope>
		</dependency>
```

### Criando classe de teste:

![Untitled](TDD%20e%20Java%20Testes%20automatizados%20com%20JUnit%20b458dc15c5014a02983679225f1e1c6c/Untitled.png)

![Untitled](TDD%20e%20Java%20Testes%20automatizados%20com%20JUnit%20b458dc15c5014a02983679225f1e1c6c/Untitled%201.png)

![Untitled](TDD%20e%20Java%20Testes%20automatizados%20com%20JUnit%20b458dc15c5014a02983679225f1e1c6c/Untitled%202.png)

A classe criada, iremos criar metodos que vão se basear nas possibilidades

```java
@Test
	void bonusDeveriaSerZeroParaFuncionarioComSalarioMuitoAlto() {
		BonusService service = new BonusService();
		BigDecimal bonus = service.calcularBonus(new Funcionario("Rodrigo", LocalDate.now(), new BigDecimal("25000")));
		
		assertEquals(new BigDecimal("0.00"), bonus);
		
	}
```

```java
	@Test
	void bonusDeveriaSer10PorCentoDoSalario () {
		BonusService service = new BonusService();
  	BigDecimal bonus = service.calcularBonus(new Funcionario("Rodrigo", LocalDate.now(), new BigDecimal("2500")));
		
		assertEquals(new BigDecimal("250"), bonus); //ERRO POR CONTA DO BIGDECIMAL, CASAS DECIMAIS
		
	}
```

```java
@Test
	void bonusDeveriaSer10PorCentoDoSalarioDecimal () {
		BonusService service = new BonusService();
		BigDecimal bonus = service.calcularBonus(new Funcionario("Rodrigo", LocalDate.now(), new BigDecimal("2500")));
		
		assertEquals(new BigDecimal("250.00"), bonus);
		
	}
```

```java
@Test
	void bonusDeveriaSerDezPorCentoParaSalarioDeExatamente10000 () {
		BonusService service = new BonusService();
		BigDecimal bonus = service.calcularBonus(new Funcionario("Rodrigo", LocalDate.now(), new BigDecimal("10000")));
		
		assertEquals(new BigDecimal("1000.00"), bonus);
		
	}
```

*Os nomes dos metodos são bem descritivos para saber a situação de testes.

# Test Driven-Developement (TDD)

![Untitled](TDD%20e%20Java%20Testes%20automatizados%20com%20JUnit%20b458dc15c5014a02983679225f1e1c6c/Untitled%203.png)

Nós basicamente iremos iniciar nosso codigo pelas classe de teste

![Untitled](TDD%20e%20Java%20Testes%20automatizados%20com%20JUnit%20b458dc15c5014a02983679225f1e1c6c/Untitled%204.png)

Poderia criar uma classe parecida, chamada `ReajusteService`. Então se vou ter uma classe `ReajusteService`, a classe de teste vai se chamar `ReajusteServiceTest` (iniciaremos por ela)

Nós precisamos conhecer a funcionalidade e pensar nos cenários. A princípio, já temos três cenários possíveis

Vamos implementar esse primeiro cenário. Preciso ter um método anotado com `@Test`

```java
public class ReajusteServiceTest {
	
	@Test
	public void reajusteDeveriaSerDeTresPorCentoQuandoDesempenhoForADesejar () {
		ReajusteService service = new ReajusteService();
		Funcionario funcionario = new Funcionario("Ana", LocalDate.now(), new BigDecimal("1000.00"));
		
		service.concederReajuste(funcionario, Desempenho.A_DESEJAR);
		
		assertEquals(new BigDecimal("1030.00"), funcionario.getSalario());
		
		
	}

}
```

Agora é que eu começo a fazer a implementação, e posso usar os atalhos da IDE para não ter que criar tudo isso na mão.

Vou passar o mouse no reajuste *service* e ira criar a classe

```java
package br.com.alura.tdd.service;

public class ReajusteService {

}
```

Está dando erro agora na classe desempenho, no *enum*, não existe esse enum. Passei o mouse, `create enum`, vou criar esse *enum.* e logo apos iremos na constante **A_DESEJAR para criar

```java
package br.com.alura.tdd.modelo;

public enum Desempenho {
	A_DESEJAR;

}
```

Agora está dando erro, tem a classe *service*, mas não tem o método. "Ctrl + 1", Eclipse, gera para mim esse método.

```java
public class ReajusteService {

	public void concederReajuste(Funcionario funcionario, Desempenho desempenho) {

	}

}
```

Agora se você reparar está tudo compilando. Se está tudo compilando já posso rodar o teste. Se eu rodar esse teste vai passar ou vai falhar? É óbvio que vai falhar. E ele dá a mensagem, esperava 1030, mas veio mil.

![Untitled](TDD%20e%20Java%20Testes%20automatizados%20com%20JUnit%20b458dc15c5014a02983679225f1e1c6c/Untitled%205.png)

O código está errado, eu estou chamando o `concederReajuste`, mas o salário não mudou, continuou mil. Por que falhou? Óbvio que não implementei. Agora é que vou implementar o algoritmo. O teste serviu como um rascunho, como um template para ir criando o meu código, para ir gerando o design de como quero o meu código.

Agora é que estou naquela etapa do TDD onde vou implementar. Então vou implementar. A ideia é que tem três cenários, mas eu estou nesse primeiro cenário, então não interessa esses outros dois cenários, vamos focar no primeiro cenário. Imagine que não existem os outros cenários. Quando eu escrever o teste dos outros cenários eu implemento o código dos outros cenários, mas vamos fingir que só existe o primeiro cenário.

```java
public class ReajusteService {

	public void concederReajuste(Funcionario funcionario, Desempenho desempenho) {
		if (desempenho == Desempenho.A_DESEJAR) {
			BigDecimal reajuste = funcionario.getSalario().multiply(new BigDecimal("0.03"));
			funcionario.reajustarSalario(reajuste);
		}
	}

}
```

no funcionário precisaremos criar um método, `reajustarSalario,` vou dar "Ctrl + 1", pedir para ele criar. Criou o método. Agora o que eu tenho que fazer? Tenho que pegar o salário atual e adicionar esse valor do reajuste.

```java
public void reajustarSalario(BigDecimal reajuste) {
		this.salario = this.salario.add(reajuste).setScale(2, RoundingMode.HALF_UP);

	}
```

*lembrando do .setScale(2, RoundingMode.HALF_UP);  que serve para definir a casa decimal e arredondamento*

A implementação completa ficou assim:

```java
public void concederReajuste(Funcionario funcionario, Desempenho desempenho) {
		if (desempenho == Desempenho.A_DESEJAR) {
			BigDecimal reajuste = funcionario.getSalario().multiply(new BigDecimal("0.03"));
			funcionario.reajustarSalario(reajuste);
		}
		
		if (desempenho == Desempenho.BOM) {
			BigDecimal reajuste = funcionario.getSalario().multiply(new BigDecimal("0.15"));
			funcionario.reajustarSalario(reajuste);
		}
		
		if (desempenho == Desempenho.OTIMO) {
			BigDecimal reajuste = funcionario.getSalario().multiply(new BigDecimal("0.20"));
			funcionario.reajustarSalario(reajuste);
		}
		
	}
```

Podemos Refatorar para o código ficar mais limpo e manutenível

para fazer isso, tiraremos esses if’s e teremos uma outra abordagem, iniciando pelos ENUM’S, ficará assim:

```java
package br.com.alura.tdd.modelo;

import java.math.BigDecimal;

public enum Desempenho {
	A_DESEJAR {
		@Override
		public BigDecimal percentualReajuste() {
			return new BigDecimal("0.03");
		}
	}, BOM {
		@Override
		public BigDecimal percentualReajuste() {
			return new BigDecimal("0.15");
		}
	}, OTIMO {
		@Override
		public BigDecimal percentualReajuste() {
			return new BigDecimal("0.2");
		}
	};

	public abstract BigDecimal percentualReajuste();
	
}
```

e nosso metodo de concederReajuste ficará assim :

```java
public class ReajusteService {

	public void concederReajuste(Funcionario funcionario, Desempenho desempenho) {
		BigDecimal percentual = desempenho.percentualReajuste();
		BigDecimal reajuste = funcionario.getSalario().multiply(percentual);
		funcionario.reajustarSalario(reajuste);
		
	}

}
```

O código agora foi refatorado, fizemos algo melhor, com isso as possibilidades de manutenção são maiores, se fosse adicionar um novo desempenho era só adicionar no enum, ou se fosse modificar algum valor, bastaria modificar no enum, com isso removemos aqueles nossos If’s.

## Vantagens TDD:

- Código já sai testado
- Evita testes “viciados” na implementação
- Refatoração faz parte do processo
- Ajuda a manter o foco
- Temos uma “tendência” em escrever um código mais simples

# Lidando com Exceptions

```java
public class BonusService {

	public BigDecimal calcularBonus(Funcionario funcionario) {
		BigDecimal valor = funcionario.getSalario().multiply(new BigDecimal("0.1"));
		if (valor.compareTo(new BigDecimal("1000")) > 0) {
			**throw new IllegalArgumentException("Funcionario com salario maior do que R$10000 nao pode receber bonus!");**
		}
		return valor.setScale(2, RoundingMode.HALF_UP);
	}

}
```

em vez de ter um bonus ZERO, lançaremos uma exception.

Quando rodarmos os testes? Provavelmente vai falar. porque quando chamar essa linha, ele lançou exception e o teste não estava esperando por essa exception, então o JUnit marca que o teste falhou.

para o JUnite entender que espera uma exception o codigo ficara assim:

```java
@Test
	void bonusDeveriaSerZeroParaFuncionarioComSalarioMuitoAlto() {
		BonusService service = new BonusService();
		assertThrows(IllegalArgumentException.class,
				() -> service.calcularBonus(new Funcionario("Rodrigo", LocalDate.now(), new BigDecimal("25000"))));
		
	}
```

JUnit possui o assertThrows

outra maneira também seria usando o Try/catch 

```java
@Test
	void bonusDeveriaSerZeroParaFuncionarioComSalarioMuitoAlto() {
		BonusService service = new BonusService();
//		assertThrows(IllegalArgumentException.class,
//				() -> service.calcularBonus(new Funcionario("Rodrigo", LocalDate.now(), new BigDecimal("25000"))));
		
		try {
			service.calcularBonus(new Funcionario("Rodrigo", LocalDate.now(), new BigDecimal("25000")));	
		} catch (Exception e) {
		
		}
		
	}
```

se eu quiser verificar se a mensagem que esta é igual :

```java
@Test
	void bonusDeveriaSerZeroParaFuncionarioComSalarioMuitoAlto() {
		BonusService service = new BonusService();
//		assertThrows(IllegalArgumentException.class,
//				() -> service.calcularBonus(new Funcionario("Rodrigo", LocalDate.now(), new BigDecimal("25000"))));
		
		try {
			service.calcularBonus(new Funcionario("Rodrigo", LocalDate.now(), new BigDecimal("25000")));	
		} catch (Exception e) {
			assertEquals("Funcionario com salario maior do que R$10000 nao pode receber bonus!", e);
		}
		
	}
```

Refatorando código de teste:

```java
public class ReajusteServiceTest {
	
	private ReajusteService service;
	private Funcionario funcionario;

	@BeforeEach
	public void inicializar () {
		this.service = new ReajusteService();
		this.funcionario = new Funcionario("Ana", LocalDate.now(), new BigDecimal("1000.00"));
	}
	
	@Disabled
	public void reajusteDeveriaSerDeTresPorCentoQuandoDesempenhoForADesejar () {
		service.concederReajuste(funcionario, Desempenho.A_DESEJAR);
		assertEquals(new BigDecimal("1030.00"), funcionario.getSalario());
		
	}

	
	@Disabled
	public void reajusteDeveriaSerDeQuinzePorCentoQuandoDesempenhoForBom () {
		service.concederReajuste(funcionario, Desempenho.BOM);
		assertEquals(new BigDecimal("1150.00"), funcionario.getSalario());
		
		
	}

	@Test
	public void reajusteDeveriaSerDeVintePorCentoQuandoDesempenhoForOtimo () {
		service.concederReajuste(funcionario, Desempenho.OTIMO);
		assertEquals(new BigDecimal("1200.00"), funcionario.getSalario());
		
		
	}

}
```

```java
@BeforeEach
	public void inicializar () {
		this.service = new ReajusteService();
		this.funcionario = new Funcionario("Ana", LocalDate.now(), new BigDecimal("1000.00"));
	}
	
	@AfterEach
	public void finalizar() {
		System.out.println("Fim");
	}
	
	@BeforeAll
	public static void antesDeTodos() {
		System.out.println("Antes");
	}
	
	@AfterAll
	public static void depoisDeTodos() {
		System.out.println("Depois de Todos");
	}
```

# O que testar?

eu devo também criar uma classe de teste para o funcionário? Qual classe eu tenho que testar? A ideia é que em um projeto você não vai testar todas as classes, todos os métodos, isso não faz sentido. Isso é algo improdutivo e algo desnecessário, porque nem tudo faz sentido você testar, nem tudo tem complexidade, não há necessidade de você testar tudo.

Você só deve testar numa aplicação classes como nossa classe `BonusService` e `ReajusteService`. É uma classe que tem uma regra de negócio, tem alguma coisa importante para o nosso sistema, tem validações, cálculos, algoritmos, e principalmente coisas que tendem a mudar com frequência no futuro.

Igual à regra do reajuste, pode ser que esse reajuste mude no futuro. Mude a alíquota, tenha novos valores aqui para reajustes. Esse é um tipo de classe que tende a sofrer bastante alteração, e que tem uma regra de negócio. Essa é a classe candidata para você testar. É exatamente esse tipo de classe que você deve testar numa aplicação, classes que tenham regras de negócio e que costumem ter muita mudança, que provavelmente vai ter alterações constantes no futuro.

Então não faz sentido testar tudo no projeto, você não deve testar todas as classes, não deve testar *getter* e *setter*, construtor.
