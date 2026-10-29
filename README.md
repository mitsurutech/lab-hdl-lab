# Digital design lab for classrooms: Verilog, VHDL and yosys in an Ubuntu VM

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-hdl-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-hdl-lab/actions/workflows/check.yml)

A ready-made **digital design lab** for teaching Verilog and VHDL, from the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning), where schools and colleges give every student their own machines and terminal in the browser. Each student gets an Ubuntu VM with **Icarus Verilog**, **GHDL** and **yosys** installed from Ubuntu's archive. They simulate circuits with a testbench, read a truth table and a **waveform** as text, finish a full adder in Verilog and in VHDL and a 4-bit counter with an enable and a synchronous reset, and count the gates yosys makes from it. Nothing is installed on the student's computer and no board is needed.

## What the student does

The lab leaves a folder `~/hdl` of files to work on and a `README.md` in the home folder that says what to do and gives none of the answers. It has five parts:

1. A combinational circuit in Verilog: read the truth table of a given half adder, then write `full_adder.v`.
2. A clock and a waveform: a given counter records its signals in a VCD file, read as rows of text with `vcd2txt` or drawn as a picture with `vcd2svg` and opened on the lab's port 8000.
3. The same full adder in VHDL with GHDL's three steps, analyse, elaborate and run: `full_adder.vhd`.
4. A counter with an enable and a synchronous reset, finished by the student: `counter4.v`.
5. Gates: `hdl gates counter4.v counter4` has yosys count the cells a design needs, which shows that a flip-flop remembers a bit and a latch is a mistake.

`hdl reset FILE` puts a file back as it was given. A Basics lesson on the platform works in this lab and leaves the full adder and the counter to the student.

## How it is marked

The lab has a **check**, a script that marks a handed-in VM without the teacher reading it. It runs on a copy of the saved machine after a fresh start. It needs `full_adder.v`, `full_adder.vhd` and `counter4.v` in `~/hdl` and refuses a Verilog file that calls a system task which ends or leaves the simulation (`$finish`, `$stop`, `$fatal`, `$fopen`, `$system` and others, comments too) and a VHDL file that uses `textio`, `std.env` or `severity failure`. Then it compiles each design with **testbenches the student never sees**. Each holds a reference model and compares it with the design, the counter before and after every clock edge, so a reset that is not synchronous is caught. A testbench prints a result line with a number the check draws afresh each run, so a design cannot print the right line itself. Last, yosys must read the designs with **no latch**, and the counter needs flip-flops and no more cells than a plain design. The first line of what it prints says what is not right.

What it does not do: it does not read the waveforms or the answers to the questions in the README, it does not place or route anything for a board, and a student with root on their own VM could still tamper with the simulators. It is a check for a classroom, not a proof.

## What is in the file

`hdl-lab.lab.yaml` is a whole **VM lab definition**, exactly what the platform runs:

| Key | What it is |
| --- | --- |
| `cpus`, `memory`, `disk` | the size of each student's VM: 1 CPU, 1024 MB and 10 GB |
| `open` | the ports that open in the browser: 8000, which shows the waveform pictures the lab draws; everything else is the terminal |
| `setup` | a script run once, as root, the first time each student's VM starts: it installs `iverilog`, `ghdl` and `yosys` from Ubuntu's archive (a few minutes), writes the helper commands `hdl`, `vcd2txt` and `vcd2svg`, the files to work on, the README, and a systemd unit that serves the pictures |
| `check` | the marking script, run as root on a copy of the saved disk after a fresh boot; its exit status is the result and `0` passes |

## Using it on the platform

**To set it as work for a class:**

1. In your module, add a task and choose the **Ubuntu VM lab** card.
2. Press the Digital design sample, or paste the contents of `hdl-lab.lab.yaml`. Using a sample replaces the text, and Undo brings yours back.
3. Publish and try your own copy first. Every student gets a VM of their own, kept between sessions.
4. Assign it like any task. When it is handed in, run the check on a student's row in the class list: it starts a copy of their saved machine that is not saved back, and shows whether it passed and what it printed. The student is not told the result.

The help manual's [page for this lab](https://mitsurutech.co.uk/help/lab-hdl-lab), its [guide to Ubuntu VM labs](https://mitsurutech.co.uk/help/labs) and its [sample labs page](https://mitsurutech.co.uk/help/sample-labs) cover the rest.

No college account yet? [Your first year is free](https://mitsurutech.co.uk/pricing) for up to 50 students, and [signing up](https://mitsurutech.co.uk/signup) takes an email address.

## Checking the file

On every push, `.github/workflows/check.yml` checks that the file is valid YAML with the keys `cpus`, `memory`, `disk`, `setup` and `check`, and that `setup` and `check` are each inside the platform's 16 KB limit. It does not boot a VM: that is done on the platform's own machines. The setup, the Basics lesson's commands and the check were run in an Ubuntu 26.04 container standing in for the VM, so one try in a booted student VM before a class is worth it.

## What does not work in a lab

The VM reaches Ubuntu's package archive, PyPI, npm and GitHub, and nothing else. Everything this lab needs comes from Ubuntu's archive. There is no desktop in a terminal VM, so there is no graphical waveform viewer: waveforms are read as text or as a picture on port 8000. Ubuntu 26.04 gives GHDL with the GCC backend, so `ghdl -e` writes an executable beside the files.

## About

Made by [Mitsuru Technologies](https://mitsurutech.co.uk) for the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning): Python, Java, Go, Node, SQL and Linux in the browser for schools and colleges, with container labs, VM labs, web apps deployed from GitHub, and quizzes that mark themselves. Try code with no account in the [playground](https://mitsurutech.co.uk/playground).

More labs from the same place: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-reverse-lab](https://github.com/mitsurutech/lab-reverse-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-tofu-lab](https://github.com/mitsurutech/lab-tofu-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) and [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Licence

This work is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). You may share it and adapt it for learning and teaching, if you credit Mitsuru Technologies Ltd, do not use it commercially, and share anything you make from it under the same licence. The full text is in the `LICENSE` file.

[Icarus Verilog](https://steveicarus.github.io/iverilog/) (GPL-2.0 or later), [GHDL](https://github.com/ghdl/ghdl) (GPL-2.0 or later) and [yosys](https://yosyshq.net/yosys/) (ISC) are installed from Ubuntu's archive when a VM starts; nothing of them is distributed here.
