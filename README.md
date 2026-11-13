# perfect_hits

**Find BLAST nucleotide alignments with 100% identity spanning the full query length.**

For protein-to-genome candidate searches using `tblastn`, see the separate [find_homologs](https://github.com/hayannacho/find_homologs) coursework repository.

## What the script does

`find_perfect_matches.sh` runs `blastn` directly against a subject FASTA file, keeps rows where `pident == 100` and `length == qlen`, writes those rows to the requested output, and prints the retained rows and their count.

No prebuilt BLAST database is needed. Inputs are nucleotide FASTA files; the subject can be an assembly or another nucleotide sequence collection.

## Requirements and execution

Use Bash with NCBI BLAST+ (`blastn`), `awk`, `wc`, and `cat` available on `PATH`. Verify BLAST installation with `blastn -version`.

```sh
git clone https://github.com/hayannacho/perfect_hits.git
cd perfect_hits
bash find_perfect_matches.sh query.fna assembly.fna matches.tsv
```

Supply exactly three paths: query FASTA, subject FASTA, and output TSV. Calling the script through `bash` avoids requiring an executable permission bit.

## Reproducible synthetic example

These artificial sequences are only an execution example, not a biological dataset. From the repository directory:

```sh
mkdir -p demo-run
cd demo-run
cat > query.fna <<'FASTA'
>query_demo
TGGGCGAACTTGGTCACCCCGAAGTATCTGATGAGATGATCACCGAGAGCCGGGGCGAGGAAGATGTACGGATACTTTCCGCACAGGGACTAGGTTAACCGCGATTTCTTATCCTGCGATAGCCGGCCGTGTAAACCTTTCTTAGGCATGGCAGAAAATG
FASTA
cat > subject.fna <<'FASTA'
>subject_demo
TGGGCGAACTTGGTCACCCCGAAGTATCTGATGAGATGATCACCGAGAGCCGGGGCGAGGAAGATGTACGGATACTTTCCGCACAGGGACTAGGTTAACCGCGATTTCTTATCCTGCGATAGCCGGCCGTGTAAACCTTTCTTAGGCATGGCAGAAAATG
FASTA
bash ../find_perfect_matches.sh query.fna subject.fna matches.tsv
```

With BLAST+ 2.12.0+, the example produced one retained row:

```text
query_demo  subject_demo  100.000  160  160  160
```

The actual output is tab-delimited. Columns are:

| Column | Meaning |
| --- | --- |
| `qseqid` | Query sequence identifier |
| `sseqid` | Subject sequence identifier |
| `pident` | Percentage of identical aligned bases |
| `length` | Alignment length |
| `qlen` | Full query length |
| `slen` | Full subject length |

The terminal also reports `Number of perfect matches: 1`. This counts retained BLAST alignment rows, not unique genes, query sequences, or subject sequences.

## Behavior and limits

- `blast_results.txt` is created or overwritten in the current working directory; the requested output is also overwritten. Use a separate run directory and an output filename different from the inputs and `blast_results.txt`.
- Input/output variables are unquoted in the original script. Use paths without spaces or shell wildcard characters.
- The script does not validate arguments or stop reliably after a failed BLAST command. Check that inputs exist and inspect BLAST errors; an empty result alone does not prove that no matches exist.
- Default BLAST search settings apply. This is a filter over reported alignments, not an exhaustive exact-string search; short or low-complexity queries may not produce expected hits.
- Output omits alignment coordinates and strand. It does not extract sequences, identify annotated genes, or establish biological homology.

## Coursework

Individual coursework — BIOL 7200, Fall 2024. I implemented the Bash workflow combining nucleotide BLAST search, exact-match filtering and result counting.

The existing commit `590e6a2` records the script addition under `hayannacho`. The demonstrated work is a compact Bash workflow combining BLAST search, `awk` filtering, and result counting. No claim is made that the BLAST algorithm, all course materials, or every line of the script was independently authored by the portfolio owner.

## Verification

On 2026-11-13, Bash syntax checking and the synthetic example above passed in a Linux container with BLAST+ 2.12.0+. This execution verifies the perfect-match script in this repository. This does not validate arbitrary biological inputs. Only this README was added; the coursework script was not changed.
