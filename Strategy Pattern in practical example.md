## Strategy Pattern in practice - how to replace a five-case switch

To avoid using a five-option switch statement between your classes, you can leverage the Strategy Pattern. This design pattern allows you to define a family of algorithms, encapsulate each one, and make them interchangeable. Here's how you can implement it:
- Define the Strategy Interface:
```csharp
public interface MessageProcessor {
	void processMessage(String message);
}
```

- Implement the Concrete Strategies:
```csharp
public class StrategyA implements MessageProcessor {
	@Override
	public void processMessage(String message) {
    	// Implementation for Strategy A
	}
}

public class StrategyB implements MessageProcessor {
	@Override
	public void processMessage(String message) {
    	// Implementation for Strategy B
	}
}

// Similarly, implement StrategyC, StrategyD, and StrategyE
```

- Create a Context Class:
```csharp
public class MessageProcessorContext {
	private MessageProcessor strategy;

	public void setStrategy(MessageProcessor strategy) {
    	this.strategy = strategy;
	}

	public void executeStrategy(String message) {
    	strategy.processMessage(message);
	}
}
```

- Use the Context Class:
```csharp
public class Main {
	public static void main(String[] args) {
    	MessageProcessorContext context = new MessageProcessorContext();

    	// Set the strategy dynamically
    	context.setStrategy(new StrategyA());
    	context.executeStrategy("Message for Strategy A");

    	context.setStrategy(new StrategyB());
    	context.executeStrategy("Message for Strategy B");

    	// Continue for other strategies
	}
}
```

By using the Strategy Pattern, you can dynamically change the strategy at runtime without needing a switch statement. This approach adheres to the Open/Closed Principle of SOLID design principles, making your code more maintainable and scalable.
