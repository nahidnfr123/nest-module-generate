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

  // generate modules in batches
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

You can customize any template by editing the `.stub` files in the `templates/` directory after running `nest-module init`.

### Customizing Existing Templates

1. **Run init to create default templates:**
   ```bash
   nest-module init
   ```

2. **Edit any `.stub` file in the `templates/` directory:**
   ```bash
   templates/
   ├── module.stub
   ├── controller.stub
   ├── service.stub
   ├── create-dto.stub
   ├── update-dto.stub
   └── schema.stub
   ```

3. **Example: Customize the service template**

   Edit `templates/service.stub` - the file should contain EXACTLY this text with the double curly braces:

   ```typescript
   import { Injectable, NotFoundException } from '@nestjs/common';
   import { InjectModel } from '@nestjs/mongoose';
   import { Model } from 'mongoose';
   import { {{SingularName}} } from './schema/{{singularName}}.schema';
   
   @Injectable()
   export class {{ModuleName}}Service {
     constructor(
       @InjectModel({{SingularName}}.name) 
       private {{camelSingularName}}Model: Model<{{SingularName}}>,
     ) {}
     
     // Add your custom methods here
     async customMethod() {
       // Your custom logic
     }
   }
   ```

   **Important:** The placeholders like `{{SingularName}}` should be typed exactly as shown - with double curly braces. When you generate a module called "users", these will be automatically replaced:
    - `{{SingularName}}` → `User`
    - `{{singularName}}` → `user`
    - `{{ModuleName}}` → `Users`
    - `{{camelSingularName}}` → `user`

4. **Use placeholders anywhere in your templates:**
    - All placeholders listed in the "Template Placeholders" section work in any template
    - Mix and match naming conventions as needed
    - Add your own custom code around the placeholders

### Adding New Custom Templates

1. **Create a new `.stub` file:**
   ```bash
   # Create a repository template
   touch templates/repository.stub
   ```

2. **Add content with placeholders:**

   Create `templates/repository.stub` with EXACTLY this content:

   ```typescript
   import { Injectable } from '@nestjs/common';
   import { InjectModel } from '@nestjs/mongoose';
   import { Model } from 'mongoose';
   import { {{SingularName}} } from './schema/{{singularName}}.schema';
   
   @Injectable()
   export class {{ModuleName}}Repository {
     constructor(
       @InjectModel({{SingularName}}.name)
       private model: Model<{{SingularName}}>,
     ) {}
     
     async findByCustomField(field: string) {
       return this.model.find({ customField: field }).exec();
     }
   }
   ```

   **Note:** Type the placeholders exactly as shown - `{{SingularName}}` with double curly braces. Your editor may show syntax errors (that's normal), but the generator will replace them correctly.

3. **Register in `generator.config.js`:**
   ```javascript
   module.exports = {
     templates: {
       // ... existing templates
       repository: {
         extension: 'repository.ts',
         required: false,
         outputPath: 'repositories',
         filename: '{{singularName}}.repository.ts'
       },
       // Add as many custom templates as you need
       interface: {
         extension: 'interface.ts',
         required: false,
         outputPath: 'interfaces',
         filename: '{{singularName}}.interface.ts'
       }
     },
     
     // Optional: exclude custom templates for specific modules
     exclusions: {
       repository: ['auth', 'health'],
       interface: ['logs']
     }
   };
   ```

4. **Generate modules with your custom templates:**
   ```bash
   nest-module generate products
   ```

   Output:
   ```
   src/modules/products/
   ├── products.module.ts
   ├── products.controller.ts
   ├── products.service.ts
   ├── dto/
   │   ├── create-product.dto.ts
   │   └── update-product.dto.ts
   ├── schema/
   │   └── product.schema.ts
   ├── repositories/
   │   └── product.repository.ts
   └── interfaces/
       └── product.interface.ts
   ```

### Template Customization Tips

- **Keep it DRY:** Use placeholders for all naming to avoid repetition
- **Use exclusions:** Skip certain templates for specific modules (e.g., no DTOs for auth)
- **Test incrementally:** Generate a test module after each template change
- **Version control:** Commit your `templates/` directory to share with your team
- **Module-specific logic:** Add conditional code in templates if needed
- **Nested directories:** Use `outputPath` to organize files in subdirectories

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT

## Author

Your Name

## Support

For issues and questions, please visit [GitHub Issues](https://github.com/yourusername/nest-module-cli/issues)