# AdventOfCode-2017
My solutions to the 2017 [Advent of Code](http://adventofcode.com/2017) challenges, dumped into one list with explanations.

Javascript solutions are executed in the browser console of the webpage that opens when you ask Advent of Code for your puzzle input.
05AB1E solutions are executed in tio.run, with the input being pasted there.

## Day 1
### Part 1
Javascript:
`document.body.innerText.replace(/[^\d]+/g,'').split('').filter((d,i,a)=>d==a[(i+1)%a.length]).reduce((t,v)=>t+parseInt(v),0)`
05AB1E:
`1ôDÀ->ΘÏ1ôO`

Takes the text from the page, keeps only the numbers, splits into an array, keeps only the items where the next one in the list (wrapping from end to front if needed) is the same, and sums what remains.
### Part 2
`document.body.innerText.replace(/[^\d]+/g,'').split('').filter((d,i,a)=>d==a[(i+a.length/2)%a.length]).reduce((t,v)=>t+parseInt(v),0)`

Same trick, but look ahead by half of the length of the list, rather than looking one element ahead.

## Day 2
### Part 1
Javascript:
`document.body.innerText.split('\n').filter(l=>!!l).map(line=>line.split(/\s+/g).reduce((t,v)=>{t.max=Math.max(t.max,v);t.min=Math.min(t.min,v);return t;},{min:Number.POSITIVE_INFINITY,max:Number.NEGATIVE_INFINITY})).map(t=>t.max-t.min).reduce((t,v)=>t+v,0)`
Turn the input into a 2D array (a matrix). Then transform each row into a {min, max} pair, transform that into max-min, and finally sum all of these values.

### Part 2
Javascript:
`document.body.innerText.split('\n').filter(l=>!!l).map(line=>line.trim().split(/\s+/g).map(v=>parseInt(v))).map(l=>l.sort((a,b)=>b-a)).map(l=>{ let d=0.5; for(let i=0;i<l.length-1&&Math.floor(d)!==d;i++){ for(let j=i+1;j<l.length&&Math.floor(d)!==d;j++) { d=l[i]/l[j]; } } return d; }).reduce((t,v)=>t+v,0);`
Turn the input into a 2D array (a matrix). Clean it up and turn each row into a sorted list of numbers. On each row, iterate from largest to smallest. Divide that number by each of the later numbers in the list. Repeat this until an even division is discovered. Now that each "row" is just the first even division result, sum them up.


## Day 3
### Part 1
Javascript:
```
manhattan=(target)=>{
  // The pattern goes: right once, up once, left twice, down twice, right three times, up three times, left four times, down four times... and so on
  // We can do some clever math with this, leveraging modular arithmetic and summation formulae.
  // First, we take the input minus one. This is how many cells to spiral. Next, we compute how far in each direction it would have to go with the above observations.
  // Then, we let left/right and up/down motion cancel out, add the absolute value of each direction, and that is our manhattan distance!
  // But before all that, let's cheat and count the slow way.
  let cell=1;step=1,dir=0;dist=[0,0,0,0]; // [right, up, left, down]
  while(cell < target) {
    let diff = Math.min(step, target-cell);
    dist[dir] += diff;
    cell += diff;
    diff = Math.min(step, target-cell);
    cell += diff;
    dir = (dir+1)%4;
    dist[dir]+=diff;
    dir = (dir+1)%4;
    step += 1;
  }
  return Math.abs(dist[0]-dist[2])+Math.abs(dist[1]-dist[3]);
};
console.log(manhattan(INPUT_NUMBER_HERE));
```

### Part 2:
Javascript:
```
spiralFill=(target)=>{
  // The pattern goes: right once, up once, left twice, down twice, right three times, up three times, left four times, down four times... and so on
  // We can do some clever math with this, leveraging modular arithmetic and summation formulae. 
  // Each cell is the sum of its neighbors, locking once set, starting from 1 and spiraling outwards as described above.
  // The neighbors include diagonals. As such, a cell can see at most four values: the one computed before it, and up to three earlier values.
  // To make searching and storage easier, we'll keep a linear array where each "cell" knows its coordinates.
  // The final output is the first cell larger than the target value when doing this.
  // There is surely a non-brute-force method that leverages clever math based on the above observations...
  let cells=[], stepSize=1, value=1, x=0, y=0, dir=0, step=1;
  while(value<=target) {
    cells.push({value,x,y});
    x += dir%2===0? [1,-1][dir/2] : 0;
    y += (dir-1)%2===0? [-1,1][(dir-1)/2] : 0;
    step--;
    if (step === 0) {
      dir = (dir+1)%4;
      if(dir===2||dir===0) stepSize++;
      step=stepSize;
    }
    let coords = [ [x-1, y], [x-1, y-1], [x, y-1], [x+1, y-1], [x+1, y], [x+1, y+1], [x, y+1], [x-1, y+1] ];
    value = cells.filter(cell => coords.find(coord => cell.x==coord[0]&&cell.y==coord[1])).reduce((t,v)=>t+v.value,0);
  }
  return value;
};
console.log(spiralFill(INPUT_NUMBER_HERE));
```

## Day 4

### Part 1
Javascript
`document.body.innerText.trim().split('\n').map(l=>l.trim().split(' ')).filter(phrase => phrase.map((w,i) => phrase.lastIndexOf(w)==i).reduce((t,v)=>t&&v,true)).length;`
Split into lines, split lines into words, transform into a true/false value (false if any word occurs twice in the line), return how many are true.

### Part 2
Javascript
`document.body.innerText.trim().split('\n').map(l=>l.trim().split(' ')).filter(phrase => phrase.map(word=>word.split('').sort().join('')).map((w,i,a) => a.lastIndexOf(w)==i).reduce((t,v)=>t&&v,true)).length`
Same deal, but also alphabetically sort each word in each phrase before checking for duplicates.

## Day 5
### Part 1
Javascript
`jumps=document.body.innerText.trim().split('\n').map(Number);index=0;steps=0;while(index<jumps.length&&index>=0) {jumps[index]++;index += jumps[index]-1;steps++;}console.log(steps);`
Pretty self-explanatory. Direct implementation of the requirements.

### Part 2
Javascript
`jumps=document.body.innerText.trim().split('\n').map(Number);index=0;steps=0;while(index<jumps.length&&index>=0) {m=jumps[index];jumps[index]=m<3?m+1:m-1;index+=m;steps++;}console.log(steps);`
Same trick, but with the extra logic for increment/decrement in it.

## Day 6
### Part 1
Javascript (set input number variable, since there was not a dedicated page for input this day)
```
let layouts = [['your','input','numbers','here']];
let steps = 0;

let noDuplicates = layout => {
  for(let r = 0; r < layouts.length; r++) {
    let dup = true;
    for(let c = 0; c < layouts[r].length; c++) {
      dup = dup && layout[c] === layouts[r][c];
    }
    if (dup) return false;
  }
  return true;
  // Copying arrays like this works but is too slow on large inputs. Thus we do it the old fashioned way.
  //let noDuplicates = layout=>!layouts.map(c=>c.map((v,i)=>v===layout[i]).reduce((t,v)=>t&&v)).reduce((t,v)=>t&&v);
}

while(true) {
  let next=Array.apply(Array,layouts[layouts.length-1]);
  let m=Math.max.apply(null, next);
  let i=next.indexOf(m);
  next[i]=0;
  while(m>0) { next[(++i)%next.length]++; m--; }
  steps++;
  if(noDuplicates(next)) { layouts.push(next); }
  else { console.log(`GOT IT, it took ${steps} steps to find a cycle.`); break; }
  if(steps > 100000) {console.log('FAILED TO FIND A CYCLE AFTER 100,000 ITERATIONS, QUITTING.');break;}
}
console.log(steps);
```

### Part 2
Javascript (same thing)
```
let layouts = [['your','input','values','here']];
let steps = 0;

let hasDuplicates = layout => {
  for(let r = 0; r < layouts.length; r++) {
    let dup = true;
    for(let c = 0; c < layouts[r].length; c++) {
      dup = dup && layout[c] === layouts[r][c];
    }
    if (dup) return r;
  }
  return false;
  // Copying arrays like this works but is too slow on large inputs. Thus we do it the old fashioned way.
  //let noDuplicates = layout=>!layouts.map(c=>c.map((v,i)=>v===layout[i]).reduce((t,v)=>t&&v)).reduce((t,v)=>t&&v);
}

while(true) {
  let next=Array.apply(Array,layouts[layouts.length-1]);
  let m=Math.max.apply(null, next);
  let i=next.indexOf(m);
  next[i]=0;
  while(m>0) { next[(++i)%next.length]++; m--; }
  steps++;
  let iter = hasDuplicates(next);
  if(iter === false) { layouts.push(next); }
  else { console.log(`Found a duplicate of row ${layouts.length} on row ${iter}, so the cycle has length ${layouts.length-iter}.`); break; }
  if(steps > 100000) {console.log('FAILED TO FIND A CYCLE AFTER 100,000 ITERATIONS, QUITTING.');break;}
}
console.log(steps);
```

## Day 7
### Part 1
Javascript
1all=document.body.innerText.trim().split('\n') .map(t => /^([a-z]+)\s\(\d+\)( -> ([a-z, ]+))?$/.exec(t.trim())) .map(l => [l[1],...(l[3]||"").split(', ')]); held = all.reduce((t,v) => [...t,...v.slice(1)], []).sort().filter((n,i,a)=>!!n&&a.lastIndexOf(n)===i); console.log(all.filter(r=>held.indexOf(r[0])===-1).map(root=>root[0]));`
### Part 2
Javascript
```
let tree = {};

document.body.innerText.trim().split('\n')
  .map(t => /^([a-z]+)\s\((\d+)\)( -> ([a-z, ]+))?$/.exec(t.trim()))
  .map(l => [l[1],Number(l[2]),...(l[4]||"").split(', ')])
  .forEach(n => tree[n[0]] = { weight:n[1],children:n.slice(2).filter(c=>!!c) });

let keys = Object.keys(tree);
let topKey = keys.filter(k => keys.filter(j => tree[j].children.indexOf(k) !== -1).length === 0)[0];
compose(topKey);
findImbalance();

function compose(k) {
  if (tree[k] && tree[k].children.length > 0) {
    tree[k].children.forEach(compose);
  }

  tree[k].sum = tree[k].weight + tree[k].children.map(j => tree[j].sum).reduce((t,v)=>t+v,0);
  tree[k].balanced = [...new Set(tree[k].children.map(j => tree[j].sum))].length <= 1;
  tree[k].output = `${tree[k].balanced?'\u2713':'\u2717'} ${k} @ ${tree[k].weight} -> ${tree[k].sum}`;
  let childOutputs = tree[k].children.map(j => tree[j].output.split('\n').map(l=>`  ${l}`).join('\n')).join('\n');
  if (childOutputs) {
    tree[k].output += '\n' + childOutputs;
  }
}

function findImbalance() {
  let inspect = topKey;
  while (!tree[inspect].balanced && tree[inspect].children.some(c=>!tree[c].balanced)) {
    inspect = tree[inspect].children.filter(c=>!tree[c].balanced)[0];
  }

  let goodValue = tree[inspect].children.map(c=>tree[c].sum).filter((v,i,a) => a.indexOf(v)!==a.lastIndexOf(v))[0];
  let badValue  = tree[inspect].children.map(c=>tree[c].sum).filter((v,i,a) => a.indexOf(v)===a.lastIndexOf(v))[0];
  let weight = tree[inspect].children.map(c=>tree[c]).filter(n=>n.sum===badValue)[0].weight;
  console.log(weight + (goodValue-badValue));
}
```

## Day 8
### Part 1
Javascript
```
let registers=document.body.innerText.trim().split('\n').reduce((t,v) => {
  let [,reg,incdec,delta,check,cmp,test] = /^([a-zA-Z]+) (inc|dec) ([-\d]+) if ([a-zA-Z]+) ([!<>=]+) ([-\d]+)$/.exec(v.trim());
  let cv = Number(t[check]||0), tv=Number(test);
  if(incdec === 'dec') delta = 0-Number(delta);
  if ( (cv>tv&&cmp=='>')||(cv<tv&&cmp=='<')||(cv==tv&&cmp=='==')||(cv!=tv&&cmp=='!=')||(cv<=tv&&cmp=='<=')||(cv>=tv&&cmp=='>=')) { t[reg]=Number(t[reg]||0)+Number(delta); }
  return t;
}, {});
Object.keys(registers).reduce((max,key)=>Math.max(max,registers[key]),Number.NEGATIVE_INFINITY)
```
Almost verbatim implementation of the requirements to calculate the value of every register, then a reduce to find the largest one's value.
### Part 2
Javascript
```
document.body.innerText.trim().split('\n').reduce((t,v) => {
  let [,reg,incdec,delta,check,cmp,test] = /^([a-zA-Z]+) (inc|dec) ([-\d]+) if ([a-zA-Z]+) ([!<>=]+) ([-\d]+)$/.exec(v.trim());
  let cv = Number(t[check]||0), tv=Number(test);
  if(incdec === 'dec') delta = 0-Number(delta);
  if ( (cv>tv&&cmp=='>')||(cv<tv&&cmp=='<')||(cv==tv&&cmp=='==')||(cv!=tv&&cmp=='!=')||(cv<=tv&&cmp=='<=')||(cv>=tv&&cmp=='>=')) { t[reg]=Number(t[reg]||0)+Number(delta); if (t[reg] > t._max) {t._max=t[reg]}; }
  return t;
}, {_max:Number.NEGATIVE_INFINITY})._max
```
