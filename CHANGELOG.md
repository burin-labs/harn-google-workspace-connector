# Changelog

## 0.1.1

- Add Gmail readonly outbound methods for message search, message retrieval, and
  attachment retrieval.
- Treat the Drive push channel token as optional setup state so outbound-only
  Google Workspace workflows can validate with just an OAuth access token.

## 0.1.0

- Initial Google Workspace connector with Drive push normalization, Drive
  changes polling, Drive/Docs outbound API dispatch, and artifact-oriented
  request helpers.
