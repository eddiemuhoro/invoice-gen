# Invoice Generator

This project is a feature-rich web application built with Vue 3 and Vite that allows users to create, customize, and download professional invoices in PDF format. It is designed to streamline the invoicing process for businesses and freelancers.

## Features

- **Dynamic Invoice Creation**: Add, edit, and remove invoice items with real-time calculations for totals.
- **Customizable Fields**: Modify company name, customer details, terms & conditions, and footer text.
- **PDF Generation**: Generate and download invoices as PDF files using `pdfmake`.
- **Responsive Design**: Optimized for both desktop and mobile devices.
- **Editable Templates**: Easily update invoice templates to match your branding.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Customize Configuration

See [Vite Configuration Reference](https://vite.dev/config/).

## Project Setup

```sh
pnpm install
```

### Compile and Hot-Reload for Development

```sh
pnpm dev
```

### Compile and Minify for Production

```sh
pnpm build
```

### Lint with [ESLint](https://eslint.org/)

```sh
pnpm lint
```

## Technologies Used

- **Vue 3**: Frontend framework for building the user interface.
- **Vite**: Fast build tool for development and production.
- **Tailwind CSS**: Utility-first CSS framework for styling.
- **pdfmake**: Library for generating PDF documents.
- **ESLint & Prettier**: Tools for maintaining code quality and formatting.

## How It Works

1. Fill in the company and customer details.
2. Add invoice items with descriptions, quantities, and unit prices.
3. Customize terms, footer text, and other fields as needed.
4. Click the "Download PDF" button to generate and save the invoice.
