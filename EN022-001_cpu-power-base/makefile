
PRJ = EN001-002_MODULO-PSU
DATE= $$(date '+%Y-%m-%d')
VER = 1.1
REL = release-${VER}-[${DATE}]

kicad-version = $$(kicad-cli version) 

all: fabrication

release: schematic fabrication

fabrication: manufacturing gerber drill bom assembly-jlcpcb

sources: release-dir release-note
	echo "\n## Project Source Files (PDF)\n" >> ./${REL}/release_notes.md

	cp ${PRJ}.kicad_sch ${REL}/sources
	cp ${PRJ}.kicad_pro ${REL}/sources
	cp ${PRJ}.kicad_pcb ${REL}/sources
	cp pagelayout_ev_6.kicad_wks ${REL}/sources
	cp makefile ${REL}/sources

schematic: release-dir release-note
	echo "\n## SCHEMATIC (PDF)\n" >> ./${REL}/release_notes.md
	
	kicad-cli sch export pdf -o ${REL}/${PRJ}_V${VER}_SCH.pdf \
	-D Revision=1.2 --no-background-color \
	${PRJ}.kicad_sch >> ./${REL}/release_notes.md 

gerber: release-dir release-note 
	echo "\n## GERBERS \n" >> ./${REL}/release_notes.md

	kicad-cli pcb export gerbers -o ./${REL}/fabrication/gerber/ \
	-l F.Cu,B.Cu,F.Paste,B.Paste,F.Silkscreen,B.Silkscreen,F.Mask,B.Mask,Edge.Cuts \
	--no-x2	--no-netlist --exclude-value --precision 6 \
	${PRJ}.kicad_pcb >> ./${REL}/release_notes.md

drill: release-dir release-note 
	echo "\n## DRILL holes \n" >> ./${REL}/release_notes.md

	kicad-cli pcb export drill -o ./${REL}/fabrication/gerber/ \
	--drill-origin absolute --gerber-precision 6 \
	--format excellon --excellon-oval-format alternate --excellon-zeros-format decimal --excellon-units mm --excellon-separate-th  \
	--generate-map --map-format gerberx2 \
	${PRJ}.kicad_pcb >> ./${REL}/release_notes.md

bom: release-dir release-note 
	echo "\n## Bill of Materials (CVS)\n" >> ./${REL}/release_notes.md

	kicad-cli sch export bom -o ${REL}/fabrication/${PRJ}_V${VER}_BOM.csv \
	--group-by Value --fields 'Reference,Value,Case,MNFR-1,MNFR-1-PN,SUPP-1,SUPP-1-CODE,SUPP-1-UNIT-PRICE ,$${QUANTITY},$${DNP}' \
	${PRJ}.kicad_sch >> ./${REL}/release_notes.md 

manufacturing: release-dir release-note 
	echo "\n## Manufacturing Instructions \n" >> ./${REL}/release_notes.md

	kicad-cli pcb export pdf -o ${REL}/fabrication/${PRJ}_V${VER}_MNF.pdf \
	-l F.Fab \
	--include-border-title --exclude-value \
	${PRJ}.kicad_pcb >> ./${REL}/release_notes.md

assembly-jlcpcb: release-dir release-note 
	echo "\n## Assembly - Centroid files \n" >> ${REL}/release_notes.md

	kicad-cli pcb export pos -o ${REL}/fabrication/assembly/${PRJ}_V${VER}_ASSEMBLY.csv \
	--side front --format csv --units mm --exclude-dnp \
	${PRJ}.kicad_pcb >> ./${REL}/release_notes.md

	sed -i 's/PosX/MidX/1' ${REL}/fabrication/assembly/${PRJ}_V${VER}_ASSEMBLY.csv 
	sed -i 's/PosY/MidY/1' ${REL}/fabrication/assembly/${PRJ}_V${VER}_ASSEMBLY.csv 
	sed -i 's/Rot/Rotation/1' ${REL}/fabrication/assembly/${PRJ}_V${VER}_ASSEMBLY.csv
	sed -i 's/Side/Layer/1' ${REL}/fabrication/assembly/${PRJ}_V${VER}_ASSEMBLY.csv
	sed -i 's/Ref/Designator/1' ${REL}/fabrication/assembly/${PRJ}_V${VER}_ASSEMBLY.csv 

3d-models:	release-dir release-note 
	echo "\n## 3D Model (STEP) \n" >> ./${REL}/release_notes.md
	
	kicad-cli pcb export step -o ${REL}/${PRJ}_V${VER}_3DMODEL.step  \
	--grid-origin --no-unspecified --subst-models --include-tracks --include-zones \
	${PRJ}.kicad_pcb >> ./${REL}/release_notes.md

release-note: release-dir release-note 
	touch ./${REL}/release_notes.md
	echo "# ${PRJ} Release V${VER}" > ./${REL}/release_notes.md
	echo "Release Date: ${DATE}   " >> ./${REL}/release_notes.md
	echo "Kicad Version: ${kicad-version}">> ./${REL}/release_notes.md

release-dir: 
	mkdir -p ${REL}
	mkdir -p ${REL}/fabrication
	mkdir -p ${REL}/fabrication/gerber	
	mkdir -p ${REL}/fabrication/assembly
	mkdir -p ${REL}/sources

clean:
	rm -rf release*

clean-all:
	rm -rf ${REL}

.PHONY: clean clean-all release-dir release-note sources fabrication release