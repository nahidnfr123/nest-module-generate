# Nest Module CLI

A powerful CLI tool to generate NestJS modules with customizable templates for modules, controllers, services, DTOs, and schemas.

## Features

- 🚀 Quick module generation with a single command
- 📝 Customizable templates using stub files
- 🎯 Automatic pluralization/singularization
- 🔧 Configurable exclusions per module
- 📦 Batch generation support
- 🎨 Multiple naming conventions (PascalCase, camelCase, kebab-case, snake_case)

## Installation

### Global Installation (Recommended)

```bash
npm install -g nest-module-cli
```

### Local Installation

```bash
npm install --save-dev nest-module-cli
```

## Usage

### Initialize Templates

First, initialize the templates directory:

```bash
nest-module init
```

This creates a `templates/` folder with default stub files for:
- Module
- Controller
- Service
- Create DTO
- Update DTO
- Schema (Mongoose)

### Generate a Module

Generate a single module:

```bash
nest-module generate users
```

Generate multiple modules:

```bash
nest-module generate users products orders
```

### Batch Generation

Generate all modules defined in your config:

```bash
nest-module batch
```

### Using npm Scripts

Add to your `package.json`:

```json
{
  "scripts": {
    "generate": "nest-module generate",
    "generate:init": "nest-module init",
    "generate:batch": "nest-module batch"
  }
}
```

Then run:

```bash
npm run generate users
npm run generate:batch
```

## Configuration

Create a `generator.config.js` file in your project root:

```javascript
module.exports = {
  templatesDir: './templates',
  outputDir: './src/app',
  
  templates: {
    module: { extension: 'module.ts', required: true },
    controller: { extension: 'controller.ts', required: true },
    service: { extension: 'service.ts', required: true },
    'create-dto': {
      extension: 'dto.ts',
      required: false,
      outputPath: 'dto',
      filename: 'create-{{singularName}}.dto.ts'
    },
    'update-dto': {
      extension: 'dto.ts',
      required: false,
      outputPath: 'dto',
      filename: 'update-{{singularName}}.dto.ts'
    },
    schema: {
      extension: 'schema.ts',
      required: false,
      outputPath: 'schema',
      filename: '{{singularName}}.schema.ts'
    }
  },
  
  exclusions: {
    'create-dto': ['auth'],
    'update-dto': ['auth'],
    schema: ['auth', 'sidebar']
  },
  
  // Optional: Generate modules in batches
  batchModules: [
    // 'users',
    // 'products',
    // 'orders'
  ]
};
```

## Template Placeholders

Use these placeholders in your `.stub` files:

- `{{moduleName}}` - users
- `{{ModuleName}}` - Users
- `{{module_name}}` - users
- `{{module-name}}` - users
- `{{camelModuleName}}` - users
- `{{MODULE_NAME}}` - USERS

Singular versions:
- `{{singularName}}` - user
- `{{SingularName}}` - User
- `{{singular_name}}` - user
- `{{singular-name}}` - user
- `{{camelSingularName}}` - user
- `{{SINGULAR_NAME}}` - USER

## Generated Structure

```
src/modules/users/
├── users.module.ts
├── users.controller.ts
├── users.service.ts
├── dto/
│   ├── create-user.dto.ts
│   └── update-user.dto.ts
└── schema/
    └── user.schema.ts
```

## Custom Templates

You can customize any template by editing the `.stub` files in the `templates/` directory after running `nest-generate init`.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT

## Author

Your Name

## Support

For issues and questions, please visit [GitHub Issues](https://github.com/yourusername/nest-module-cli/issues)