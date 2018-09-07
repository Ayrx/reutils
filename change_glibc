#!/usr/bin/env python3

import click
import lief
import pathlib


@click.command(
    help="Change the linked glibc of an ELF binary."
)
@click.argument("bin", type=click.Path(exists=True))
@click.argument("libc", type=click.Path(exists=True, resolve_path=True))
@click.argument("ld", type=click.Path(exists=True, resolve_path=True))
@click.argument("out", type=click.Path())
def cli(bin, libc, ld, out):
    binary = lief.parse(bin)

    libc_name = None
    for i in binary.libraries:
        if "libc.so.6" in i:
            libc_name = i
            break

    if libc_name is None:
        click.echo("No libc linked. Exiting.")

    click.echo("Current ld.so:")
    click.echo("Path: {}".format(binary.interpreter))
    click.echo()

    libc_path = str(pathlib.Path(str(libc)).parent)

    binary.interpreter = str(ld)
    click.echo("New ld.so:")
    click.echo("Path: {}".format(binary.interpreter))
    click.echo()

    binary += lief.ELF.DynamicEntryRunPath(libc_path)
    click.echo("Adding RUNPATH:")
    click.echo("Path: {}".format(libc_path))
    click.echo()

    click.echo("Writing new binary {}".format(out))
    click.echo("Please rename {} to {}/libc.so.6.".format(
        libc, libc_path
    ))
    binary.write(out)


if __name__ == "__main__":
    cli()
