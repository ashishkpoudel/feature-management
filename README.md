# Feature Management

```
Status: Experimental
```

Feature Management (also called feature toggle, feature flag) makes it easier to enable or disable feature based on specified configuration.

## Installation

You can install the package via npm:

```
npm install feature-management
```

## Usage

Feature must be declared as a class. Feature flag configuration is then applied by using `@FeatureFlag` decorator.


## Example

### Basic usage

```typescript
@FeatureFlag('production', false)
@FeatureFlag('staging', true)
class HostReport implements IFeature {}
```

Above we've declared a feature called `HostReport` with feature flag configuration applied for multiple environment. Last step is to initialize feature manager and check if specified feature is enabled or not based on current environment.

```typescript
if (await featureManager.isEnabled(HostReport)) {
  // run this block if enabled
}
```

### Advance usage with strategies
Strategy helps you enable a feature based on condition defined. You can assign multiple strategies to a feature.

```typescript
interface FeatureManagerContext {
  readonly email?: string;
}

class AllowUsers {
  constructor(readonly emails: readonly string[]) {}
}

@StrategyHandler(AllowUsers)
class AllowUsersHandler implements IStrategyHandler {
  async evaluate(strategy: AllowUsers, context: FeatureManagerContext) {
    if (!context?.email) {
      throw new Error('AllowUsers Strategy requires param: email');
    }

    return strategy.emails.includes(context.email);
  }
}
```

Assign strategy to a feature
```typescript
@FeatureFlag('production', [new AllowUsers(['john@gmail.com', 'doe@gmail.com'])])
@FeatureFlag('staging', true)
class HostReport implements IFeature {}
```

Finally, you can check if feature is enabled using feature manager.

```typescript
const context = {
  email: 'john@gmail.com'
};

if (await featureManager.isEnabled(HostReport, context)) {
  // run this block if enabled
}
```

## Testing

```
npm run test
```

## Credits
- [Ashish K. Poudel](https://github.com/ashishkpoudel)
- [All Contributors](../../contributors)
