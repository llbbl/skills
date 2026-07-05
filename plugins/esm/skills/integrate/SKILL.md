---
name: integrate
version: 0.1.0
description: Integrate llbbl/enum-state-machine into a PHP project with enum states, attribute transitions, guards, hooks, and validation
allowed-tools: Bash, Read, Grep, Glob, Edit, MultiEdit, Write
---

# /esm:integrate

Use this skill when the user wants to add or improve `llbbl/enum-state-machine` in a PHP project, such as:

- "Add enum-state-machine to this app."
- "Model order states with ESM."
- "Convert this status string workflow to enum transitions."
- "Add guards/hooks around this state change."
- "Use llbbl/enum-state-machine in Laravel/Symfony/plain PHP."

## First Pass

1. Inspect `composer.json`, PHP version constraints, framework clues, and the existing domain model.
2. Find the state field or workflow being modeled before adding new abstractions.
3. Confirm the project can use PHP enums and attributes. The library requires PHP `>=8.1`.
4. If the dependency is missing, add it with Composer:

```bash
composer require llbbl/enum-state-machine
```

Use the project’s existing dependency workflow if it wraps Composer.

## Integration Shape

Prefer one focused enum per workflow:

```php
use EnumStateMachine\Attributes\Transition;

enum OrderState: string
{
    #[Transition(to: self::Processing)]
    case Paid = 'paid';

    #[Transition(to: self::Shipped, guard: HasValidAddress::class)]
    case Processing = 'processing';

    case Shipped = 'shipped';
    case Cancelled = 'cancelled';
}
```

Use class-level transitions for wildcard behavior:

```php
use EnumStateMachine\Attributes\Transition;

#[Transition(to: self::Cancelled)]
enum OrderState: string
{
    case Pending = 'pending';
    case Paid = 'paid';
    case Cancelled = 'cancelled';
}
```

Use `StateMachine` at the boundary where state changes happen:

```php
use EnumStateMachine\StateMachine;

$machine = new StateMachine($order->state);

if (! $machine->can(OrderState::Shipped, context: $order)) {
    // Reject or hide the transition.
}

$order->state = $machine->transitionTo(OrderState::Shipped, context: $order);
```

## Guards And Hooks

Use guards for validation that decides whether a transition is allowed. Guards must be side-effect-free because `can()` runs them:

```php
use EnumStateMachine\Contracts\GuardInterface;

final class HasValidAddress implements GuardInterface
{
    public function __invoke(object $from, object $to, mixed $context = null): bool
    {
        return $context?->shippingAddress !== null;
    }
}
```

Use hooks for side effects:

- `before` hooks run before the state changes.
- `after` hooks run after the state changes.
- If an after hook throws, the state has already changed; design persistence and retries deliberately.

## Framework Notes

- In Laravel or Symfony, keep the enum on the model/entity and call `StateMachine` from an application service, action, command handler, or controller boundary.
- If guards/hooks need dependencies, pass a PSR-11 container to `StateMachine`.
- If the app uses domain events, enable PSR-14 dispatch with `#[StateMachineConfig(dispatchEvents: true)]` and provide a dispatcher.
- Do not put persistence, emails, or external API calls in guards.

## Migration Pattern

When replacing string statuses:

1. Add a backed enum whose case values match existing stored values.
2. Update model/entity casts or accessors so the state property is enum-typed.
3. Declare only the transitions the product actually allows.
4. Replace ad hoc status assignments with `transitionTo()`.
5. Add tests for allowed transitions, rejected transitions, guard rejection, and hook side effects.

## Validation

Run the target project’s existing checks. Common PHP commands:

```bash
composer test
composer qa
vendor/bin/pest
vendor/bin/phpunit
vendor/bin/phpstan analyse
```

If no test command exists, add focused tests around the state enum and the state-changing service before changing production call sites.
