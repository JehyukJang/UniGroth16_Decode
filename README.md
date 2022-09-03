# UniGro16js
Universial Groth16 for EVM implemented in js and MATLAB

What is the universal Groth16 for EVM?

Universal Groth16 (UGro16) for EVM is a zk-SNARK that inherits the succinctness of original Groth16 while adding universality for EVM-based applications. The universality of UGro16 can be said as EVM-specific universality, meaning that a trust setup only need to be executed when an EVM is newly- or re-defined. A universal reference string file generated by a trust setup contains proving and verifying keys for each instuction of an EVM. Thus, the universal reference string can be used for all kinds of EVM applications made of the predefined instructions.

The key idea of UGro16 is Derive algorithm, which linearly combines the proving and verifying keys in a universal reference string according to the combination of instructions forming an EVM application. As the result, Derive outputs a circuit-specific reference string, which is specialized to an EVM application. The execution of Derive does not need a trust, so anyone can be an executor. Derive needs to be executed when an EVM-application is newly developed or updated. The structure of a circuit-specific reference string is almost equivalent with a common reference string of original Groth16's setup except only that UGro16 uses bivarate QAP polynomials instead of univarte ones. This enablues us to use the fastest prove and verify algorithms of the original Groth16.

Based on the same application, UGro16 might be a bit slower than Gro16 at the cost of universality. This is because we add redundant constraints to the circuits of EVM instructions to make room for combining.

Implementations (Demo. version)

Protocol composition

UGro16 consists of eight algorithms: compile, buildQAP, generateWitness, decode, setup, derive, prove, and verify.
- compile takes circom implementations of all EVM instructions as inputs and outputs respective R1CSs in \*.r1cs files, respective wasms in \*.wasm files, and an EVM information in wire_list.json file.
- buildQAP takes R1CS files and an EVM parameter s_max* as inputs and outputs respective QAP polynomials in \*.qap files
- decode(MATLAB) takes a p-code(bytecode) of an EVM application, initial storage data, and a wire_list.json as inputs and outputs instances for all instructions used in a p-code and a wire map (information of the circuit for an EVM application). The instances are divided and stored in Input_opcode#.json and Output_opcode#.json for indices # of all the instructions. The wire map is divided and stored in OpList.bin, Set_I_P.bin, Set_I_V.bin, and WireList.bin.
- setup takes R1CS files and EVM parameters as inputs and outputs a universal reference string in a \*.urs file.
- derive takes a universal reference string file and wire map filesas inputs and outputs a circuit-specific reference string in \*.crs file.
- generateWitness takes instances and wasm files for all instructions used in a p-code as inputs and outputs respective witnesses in \*.wtns files, which includes the instances.
- prove takes a circuit-specific reference string file, witness files, QAP files and wire map files as inputs and outputs a proof in \*.proof file.
- verify takes a proof file, a circuit-specific reference string file, instance files, and wire map files as inputs and prints out whether the proof is valid or not.

* EVM parameter s_max: The maximum number of arithmetic opcodes that can be contained in an EVM application.

Explanation for the inputs and outputs

- EVM instructions: Arithmetic opcodes including keccak256 in EVM from 0x01 to 0x20
- Circom implementation: A circom script of executing an opcode
- R1CS: A set of wires and constraints forming the (sub)circuit of a circom implementation (called subcircuit)
- wasm: A script of executing an opcode ported in wasm
- 

Prerequisites and preparation for use

- Implementing circoms and generating R1CSs and wasms needs to install Circom package by Iden3.
  - [How to install Circom](https://docs.circom.io/getting-started/installation/)
- Some of libraries by Iden3 are used.
  - How to install Iden3 libraries
- Prepare resources
  - 

How to use

All file names used in the following commands does not include the file name extensions (e.g., for "refstr.rs", just type "refstr")
- compile
  - [How to run compile](https://github.com/pleiadex/circom-ethereum-opcodes/blob/main/README.md)
- buildQAP
  - Enter the command "node build/cli.cjs QAP_all bn128 \[s_D, the number of arithmetic instructions in EVM] \[s_max]"
- setup
  - Enter the command "node build/cli.cjs setup param\_\[s_D]\_\[s_max] \[output rs file name] \[any number for the seed of random generation]"
- decode
  - How to run decode
- derive
  - Enter the command "node build/cli.cjs derive \[input rs file name] \[output crs file name] \[circuit (EVM application) directory name] QAP\_\[s_D]\_\[s_max]"
- generateWitness
  - In the current version, generatedWitness is called during executing prove (will be updated to be separately executed).
- prove
  - Enter the command "node build/cli.cjs prove \[input crs file name] \[output proof file name] QAP\_\[s_D]\_\[s_max] \[circuit (EVM application) directory name] \[any number for the seed of random generation] \[the index of circuit instance set]"
 -verify
  - Enter the command "node build/cli.cjs verify \[input proof file name] \[input crs file name] \[circuit (EVM application) directory name] \[the index of circuit instance set]"

Directories for the output files
- 


