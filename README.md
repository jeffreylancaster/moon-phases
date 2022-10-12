# Moon Phases
A simple calendar with phases of the moon for each day

## Inspiration/Components
- [Moonglow](https://www.moonglow.com/) jewelry's reference book.
- [D3 Heatmap Calendar](https://bl.ocks.org/micahstubbs/89c6bd879d64aa511372064c6cf85711)
- [Infinite Scroll](https://www.yogihosting.com/jquery-infinite-scroll/)

## Moon Phase Function
I rewrote the PHP moon phase calculation function from [JiveBay](https://jivebay.com/calculating-the-moon-phase/) in JavaScript and modified it to take a JavaScript `Date` object:

```javascript
function moon_phase(date){
	var d = new Date(date);
	var year = d.getFullYear();
	var month = d.getMonth()+1;
	var day = d.getDate();

	// set variables
	var c = e = jd = b = 0;

	var returnObj = {
		'name': '',
		'code': '',
		'percent': null
	}

	var phase_names = ['New Moon', 'Waxing Crescent Moon', 'Quarter Moon', 'Waxing Gibbous Moon', 'Full Moon', 'Waning Gibbous Moon', 'Last Quarter Moon', 'Waning Crescent Moon'];
	var phase_codes = ['NL', 'CA', '1A', '2A', '3A', '4A', '5A', '6A', '7A', 'PL', '7D', '6D', '5D', '4D', '3D', '2D', '1D', 'CD'];
	
	// adjust month
	if(month < 3){
		year--;
		month += 12;
	}
	
	// increment the month by 1
	month++;

	c = 365.25 * year;
	e = 30.6 * month;
	jd = c + e + day - 694039.09; // jd is total days elapsed
	jd = jd/29.5305882; //divide by the moon cycle
	b = Math.floor(jd); //int(jd) -> b, take integer part of jd
	jd -= b; //subtract integer part to leave fractional part of original jd
	

	var bPN = Math.round(jd * phase_names.length); //scale fraction from 0 to phase_names.length and round
	if(bPN >= phase_names.length){
		bPN = 0; // 0 and phase_names.length are the same so turn phase_names.length into 0
	}
	returnObj.name = phase_names[bPN];

	var bPC = Math.round(jd * phase_codes.length); //scale fraction from 0 to phase_codes.length and round
	if(bPC >= phase_codes.length){
		bPC = 0; // 0 and phase_codes.length are the same so turn phase_codes.length into 0
	}
	returnObj.code = phase_codes[bPC];

	var bPercent = 2*(0.5-Math.abs(jd-0.5));
	returnObj.percent = bPercent; 
		// 0 <= percent <= 0.5 (crescent); 0.5 <= percent <= 1 (gibbous) 

	returnObj.jd = jd;
		// 0 <= jd <= 0.5 (waxing); 0.5 < jd <= 1 (waning)

	return returnObj;
}
```

The object that is returned contains a few moon phase metrics:
```json
{
	"name": String,
	"code": String,
	"percent": Number,
	"jd": Number
}
```

`name` comes from the array: `['New Moon', 'Waxing Crescent Moon', 'Quarter Moon', 'Waxing Gibbous Moon', 'Full Moon', 'Waning Gibbous Moon', 'Last Quarter Moon', 'Waning Crescent Moon']`

`code` is the Moonglow code from: `['NL', 'CA', '1A', '2A', '3A', '4A', '5A', '6A', '7A', 'PL', '7D', '6D', '5D', '4D', '3D', '2D', '1D', 'CD']`

`percent` is the percentage of "light" coming from the moon, between 0 and 1.

`jd` is the percentage progress through the lunar cycle from new moon to new moon, between 0 and 1.