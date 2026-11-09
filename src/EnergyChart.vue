<script setup>
import { computed, useId } from 'vue'
const gradientId = useId()
const props=defineProps({mode:{default:'supply'},shifted:Boolean})
const times=['08:00','10:00','12:00','14:00','18:00','21:00']
const supply=[18,52,90,88,48,15]
const before=[75,30,28,30,64,88],after=[28,30,78,85,64,32]
const xPositions=[60,158,256,354,452,550]
const yPosition=value=>224-value*1.5
const curve=values=>{
	const firstY=yPosition(values[0])
	let path=`M ${xPositions[0]} ${firstY}`
	for(let index=0;index<values.length-1;index++){
		const middleX=(xPositions[index]+xPositions[index+1])/2
		const middleY=(yPosition(values[index])+yPosition(values[index+1]))/2
		path+=` Q ${xPositions[index]} ${yPosition(values[index])} ${middleX} ${middleY}`
	}
	const last=values.length-1
	return `${path} Q ${xPositions[last-1]} ${yPosition(values[last-1])} ${xPositions[last]} ${yPosition(values[last])}`
}
const supplyPath=curve(supply)
const areaPath=computed(()=>`${supplyPath} L ${xPositions.at(-1)} 224 L ${xPositions[0]} 224 Z`)
const beforePath=computed(()=>curve(before))
const afterPath=computed(()=>curve(after))
</script>
<template>
	<div class="chart">
		<svg viewBox="0 0 620 282" role="img" :aria-label="mode==='supply'?'Simulated renewable availability, highest at noon and 2 PM':'Illustrative original and proposed demand patterns against renewable availability'">
			<defs>
				<linearGradient :id="gradientId" x1="0" y1="0" x2="0" y2="1">
					<stop offset="0%" stop-color="#a8e881" stop-opacity=".35"/>
					<stop offset="100%" stop-color="#8ce2d2" stop-opacity=".015"/>
				</linearGradient>
			</defs>
			<rect x="207" y="46" width="98" height="178" rx="8" class="chart-window"/>
			<g class="chart-grid">
				<path v-for="y in [74,111.5,149,186.5,224]" :key="y" :d="`M60 ${y}H550`"/>
				<path v-for="x in xPositions" :key="x" :d="`M${x} 60V224`" class="chart-tick"/>
			</g>
			<text x="60" y="27" class="chart-kicker">{{mode==='supply'?'SIMULATED RENEWABLE SUPPLY':'DEMAND ALIGNMENT · ILLUSTRATIVE'}}</text>
			<text x="300" y="62" text-anchor="middle" class="chart-window-label">SOLAR WINDOW</text>
			<text v-for="(label,index) in ['100','75','50','25','0']" :key="label" x="43" :y="[78,115.5,153,190.5,228][index]" text-anchor="end" class="chart-axis">{{label}}</text>
			<path :d="areaPath" :fill="`url(#${gradientId})`"/>
			<path :d="supplyPath" class="chart-supply-glow"/>
			<path :d="supplyPath" class="chart-supply-line"/>
			<template v-if="mode!=='supply'">
				<path :d="beforePath" class="chart-before-line"/>
				<path v-if="shifted" :d="afterPath" class="chart-after-line"/>
			</template>
			<g v-for="(value,index) in supply" :key="times[index]" class="chart-points">
				<circle :cx="xPositions[index]" :cy="yPosition(value)" r="4"/>
			</g>
			<text v-for="(time,index) in times" :key="time" :x="xPositions[index]" y="256" text-anchor="middle" class="chart-time">{{time}}</text>
		</svg>
		<div class="legend">
			<span><i class="legend-supply"></i>Renewable availability</span>
			<template v-if="mode!=='supply'">
				<span><i class="legend-before"></i>Original demand</span>
				<span v-if="shifted"><i class="legend-after"></i>Proposed demand</span>
			</template>
			<template v-else><span class="legend-context">Relative index · not live grid data</span></template>
		</div>
	</div>
</template>
