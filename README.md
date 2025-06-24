# AvaliaçãoCastelApp

> App Android em React Native para registro de avaliações de frutas, com suporte offline, autenticação Firebase, exportação XLSX e envio via WhatsApp.

---

## 📋 Sumário

1. [Visão Geral](#visão-geral)  
2. [Funcionalidades](#funcionalidades)  
3. [Fluxo de Telas](#fluxo-de-telas)  
4. [Tecnologias e Dependências](#tecnologias-e-dependências)  
5. [Pré-requisitos](#pré-requisitos)  
6. [Configuração do Ambiente](#configuração-do-ambiente)  
7. [Execução em Desenvolvimento](#execução-em-desenvolvimento)  
8. [Build de Release (APK/AAB)](#build-de-release-apkaab)  
9. [Banco de Dados Local (SQLite)](#banco-de-dados-local-sqlite)  
10. [Firebase](#firebase)  
11. [Schema](#schema)  
12. [Componentização & Arquitetura](#componentização--arquitetura)  
13. [Contextos & Providers](#contextos--providers)  
14. [Entrega ao Cliente](#entrega-ao-cliente)  
15. [Contato & Suporte](#contato--suporte)  

---

## Visão Geral

O **AvaliaçãoCastelApp** permite que equipes de campo registrem e gerenciem avaliações de frutas mesmo sem conexão.  
Oferece:

- **Autenticação** Firebase (e-mail/senha) com cache offline  
- **Armazenamento local** SQLite para toda a estrutura de dados  
- **Wizard** de cadastro em múltiplas etapas  
- **Exportação** para XLSX com cabeçalhos dinâmicos  
- **Compartilhamento** formatado via WhatsApp ou e-mail  
- **Gestão** de dados mestres (produtores, variedades, locais)  

---

## Funcionalidades

1. **Login**  
   - Tela de login com logo, validação online/offline  

2. **Lista de Avaliações**  
   - FlatList com filtros por data, ticket, produtor  
   - Indicadores “Enviado WhatsApp” / “Enviado Email”  
   - Menu de ações: Nova avaliação, Exportar, Filtrar, Sair  

3. **Cadastro Wizard** (4 passos)  
   - **Identificação**: data (DatePicker), ticket → busca produtor, autocomplete  
   - **Classificação**: %CAT1/2/3/IND (soma=100%), peso, frutas miúdas, RP, BRIX (decimal)  
   - **Defeitos**: tabela dinâmica incluindo Falta de Cor, Granizo, Russeting, Sarna, Glomerela, Bichada, etc.  
   - **Finalização**: Enchimento dos Bins (Bom/Vazios), Polibolha (Ok/Está sem), Observação  

4. **Preview / Confirmação**  
   - Visualizar todos os campos  
   - Ações: enviar WhatsApp / enviar e-mail com confirmação (snackbar)  

5. **Gestão de Master Data**  
   - Tela dedicada para visualizar, editar e excluir produtores, variedades e locais  

6. **Exportação XLSX**  
   - Geração de planilha via SheetJS (`xlsx`)  
   - Cabeçalhos acentuados via `labelsEnum`  
   - Anexo em e-mail com confirmação de envio  

---

## Fluxo de Telas

1. **Login**  
2. **Lista de Avaliações**  
3. **FilterDialog** (popup)  
4. **Cadastro Wizard**  
5. **Preview / ConfirmAvaliacao**  
6. **MasterDataScreen** (produtores, variedades, locais)  

---

## Tecnologias e Dependências

- **React Native** (CLI + TypeScript)  
- **React Navigation** (Native Stack)  
- **React Native Paper** (UI, theming e Snackbar)  
- **@react-native-community/datetimepicker**  
- **@react-native-picker/picker**  
- **react-native-sqlite-storage**  
- **Firebase Auth**  
- **SheetJS (`xlsx`)**, **react-native-fs**, **react-native-mail**  

---

## Pré-requisitos

- **Node.js** ≥ 16  
- **Yarn** ou **npm**  
- **JDK 17+**  
- **Android Studio** + SDK Platform 35  
- **Variáveis**:
  ```bash
  export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
  export ANDROID_HOME=$HOME/Android/Sdk
  export PATH=$JAVA_HOME/bin:$ANDROID_HOME/emulator:$ANDROID_HOME/tools/bin:$ANDROID_HOME/platform-tools:$PATH
  ```

---

## Configuração do Ambiente

```bash
git clone <repo-url>
cd AvaliacaoCastelApp
yarn install        # ou npm install
```

---

## Execução em Desenvolvimento

```bash
npx react-native start
npx react-native run-android
yarn lint
yarn test
```

---

## Build de Release (APK / AAB)

1. **Gerar keystore**  
   ```bash
   keytool -genkeypair -v      -keystore android/app/my-release-key.keystore      -alias my-key-alias      -keyalg RSA -keysize 2048 -validity 10000
   ```
2. **Gradle properties** (`android/gradle.properties`):
   ```
   MYAPP_UPLOAD_STORE_FILE=my-release-key.keystore
   MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
   MYAPP_UPLOAD_STORE_PASSWORD=<store_password>
   MYAPP_UPLOAD_KEY_PASSWORD=<key_password>
   ```
3. **Gerar APK**:
   ```bash
   cd android
   ./gradlew assembleRelease
   ```
4. **Gerar AAB** (opcional):
   ```bash
   ./gradlew bundleRelease
   ```

---

## Banco de Dados Local (SQLite)

- **Arquivo**: `database.ts`  
- **Tabelas**:
  - `produtores`
  - `variedades`
  - `locais_armazenagem (nome TEXT PK)`
  - `avaliacoes` 

- **Carga Inicial**: script Node.js ou SQL executado após primeiro login  

---

## Firebase

- **Auth**: E-mail/Senha (`******@****.com`)

---

## Schema

```sql
CREATE TABLE produtores  (...);
CREATE TABLE variedades  (...);
CREATE TABLE locais_armazenagem (...;
CREATE TABLE avaliacoes (...);
```

---

## Componentização & Arquitetura

- **components/**  
  - `GenericTextInput.tsx`  
  - `DateInput.tsx`  
  - `PickerInput.tsx`  
  - `DefectTable.tsx`  
  - `WizardLayout.tsx`  
  - `AppMenu.tsx`, `FilterDialog.tsx`  

- **screens/**  
  - `Login`  
  - `ListaAvaliacoes`  
  - `CadastroAvaliacao`  
  - `ConfirmAvaliacao`  
  - `PreviewAvaliacao`  
  - `MasterDataScreen`  

- **services/**  
  - `dbService` (SQLite)  
  - `exportService` (XLSX / e-mail / WhatsApp)  

- **context/**  
  - `SnackbarProvider`  

- **utils/**  
  - `helper.ts`, `labelsEnum.ts`, tipos TS  

---

## Contextos & Providers

- **PaperProvider** (tema)  
- **SnackbarProvider** (feedback global)  

---

## Contato & Suporte

- **Dev Lead:** Patrick Cremonese — contato@patrickcremonese.com.br  
- **React Native:** https://reactnative.dev  
- **Firebase Auth:** https://firebase.google.com/docs/auth  

> Obrigado por confiar na nossa solução!  
> Estamos à disposição para dúvidas ou futuras melhorias.
