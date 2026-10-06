# requerimientos tecnicos

* ocupar pnpm en vez de npm
* React Router v8 (8.4.0) en modo framework (`create-react-router`): includes SSR, loaders/actions y file conventions (`routes.ts`, `root.tsx`)
* TypeScript para tipado estático y mejor desarrollo con React.
* React version 19
* MUI (Material UI) - libreria de interfaz visual (`@mui/material`, `@emotion/react`, `@emotion/styled`, `@mui/icons-material`)
* Para la conexion de servicio REST, utilizar la liberia nativa de fetch
* Para las pruebas, utilice las siguientes librerias:
  - React Testing Library (`@testing-library/react`) para pruebas de componentes React.
  - MSW (`msw`) para simular respuestas del servicio REST en pruebas.
  - Vitest (`vitest`) para pruebas unitarias y de integración.
