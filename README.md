# amplify-integration-with-nativescript-vue

## env

```bash
python~3
```

## Nativescript-vue project setup

```bash
npm install -g @vue/cli @vue/cli-init

vue init nativescript-vue/vue-cli-template <app-name>
cd <app-name>
npm install
tns run ios --debug --device 'iPhone 11 Pro'
```

## amplify setup

```bash
npm install -g @aws-amplify/cli
npm i @aws-amplify/api

amplify --version
## 4.13.1

amplify init

amplify add api
```
