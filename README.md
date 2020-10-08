# Create data structure to inform GCPN loss function using KG-COVID-19 embeddings

In this notebook, we make a data structure to be used to inform a GCPN loss function with
information from embeddings of the KG-COVID-19 knowledge graph. This graph contains a broad
array of information about COVID-19 and SARS-CoV-2 ([detailed here](https://knowledge-graph-hub.github.io/kg-covid-19-dashboard/)).

The data structure we produce will be a 2D matrix comprised of the 6,900 ChEMBL antiviral compounds as rows,
important SARS-CoV-2 entities represented as nodes in our KG (such as SARS-CoV-2 itself, protein cleavage
products like ADRP and Mpro, etc) as columns. The value in the matrix will be the cosine similarity
between the embedding of the node for the ChEMBL antiviral in the row and the embedding of the node for
the SARS-CoV-2 entity in that column. The matrix will be output as a TSV. The SMILE string representation of each ChEMBL antiviral will also be output as an additional column so that Tanimoto similarity can be calculated.

The data structure produced here will be used to investigate whether embeddings of the COVID-19 KG
can be used to guide GCPNs in producing more useful/viable antivirals for COVID-19 treatment. Specifically,
for each compound produced by GCPN, a loss function might be defined using the product of the Tanimoto similarity and cosine similarity for the ChEMBL antivirals, something like:

<a href="https://www.codecogs.com/eqnedit.php?latex=L&space;=&space;-max^{n}_{i=1}(tanimoto(C^{gcpn},&space;C^{chembl}_i)&space;*&space;cosine_sim(C^{chembl}_i,&space;N^{interest}))" target="_blank"><img src="https://latex.codecogs.com/gif.latex?L&space;=&space;-max^{n}_{i=1}(tanimoto(C^{gcpn},&space;C^{chembl}_i)&space;*&space;cosine_sim(C^{chembl}_i,&space;N^{interest}))" title="L = -max^{n}_{i=1}(tanimoto(C^{gcpn}, C^{chembl}_i) * cosine_sim(C^{chembl}_i, N^{interest}))" /></a>

where n is the number of ChEMBL antivirals, N is the SARS-CoV-2 node of interest (ADRP, Mpro, SARS-CoV-2 itself, etc) that is to be targeted by the therapeutic.

Conceptually this should confine the GCPN to some boundary of "druglikeness" as defined by similarity to ChEMBL antivirals and their ability to target the drug target of interest.

CAVEAT: These embeddings were generated using an 80/20 training/test split. We could and possibly should regenerate these embeddings with the entire graph (no 80/20 split)
