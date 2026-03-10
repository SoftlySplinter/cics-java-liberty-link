# cics-java-liberty-link

[![Build](https://github.com/cicsdev/cics-java-liberty-link/actions/workflows/java.yaml/badge.svg)](https://github.com/cicsdev/cics-java-liberty-link/actions/workflows/java.yaml)

A sample CICS Java application demonstrating how to use the `@CICSProgram` annotation to link to Enterprise Java programs running in a Liberty JVM server. This sample shows how to create linkable POJOs that can be invoked from CICS programs or transactions using standard CICS LINK commands.

## Overview

This sample demonstrates:
- Using the `@CICSProgram` annotation to expose Java methods as CICS programs
- Linking to Java applications in Liberty JVM servers from CICS
- Passing data via CICS channels and containers
- Building and deploying with Gradle, Maven, or CICS Explorer
- Creating CICS bundles for automated deployment

## Prerequisites
- **CICS TS for z/OS V5.5 or later**
- **Java SE 1.8 or later** on your workstation
- **Build tool**: Eclipse with CICS SDK, or any IDE supporting Gradle/Maven


## Documentation

**[Full Documentation](docs/README.md)** - Comprehensive guide including:
- Detailed build instructions for all methods
- Deployment options and configurations
- Liberty server setup requirements
- Troubleshooting and advanced topics


## Contributing

We welcome bug reports and feedback! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on reporting issues.

## Resources

- [IBM CICS Transaction Server for z/OS](https://www.ibm.com/products/cics-transaction-server)
- [CICS TS Documentation](https://www.ibm.com/docs/en/cics-ts/)
- [Linking to Java applications in Liberty JVM servers](https://www.ibm.com/docs/en/cics-ts/latest?topic=djariljs-linking-java-applications-in-liberty-jvm-server-by-using-cicsprogram-annotation)
- [CICS BOM on Maven Central](https://mvnrepository.com/artifact/com.ibm.cics/com.ibm.cics.ts.bom)

## Support

This sample is provided **as-is** with no warranties or support. For questions or issues, please [open an issue](https://github.com/cicsdev/cics-java-liberty-link/issues) in this repository.

## License

This project is licensed under the [Apache-2.0 License](LICENSE).

## Usage terms
By downloading, installing, and/or using this sample, you acknowledge that separate license terms may apply to any dependencies that might be required as part of the installation and/or execution and/or automated build of the sample, including the following IBM license terms for relevant IBM components:

- IBM CICS development components terms: https://www.ibm.com/support/customer/csol/terms/?id=L-ACRR-BBZLGX

---

**Copyright IBM Corp. 2016, 2026**
