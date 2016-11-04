# Custom FORMATS

## Localized plurals

### Polish

``` php
// custom format definition
$f3->set('FORMATS.polish',function($nb,$mod){
  preg_match_all('/(\d)\s*\{\s*(.+?)\s*\}/',$mod,$matches,PREG_SET_ORDER);
  $terms=[];
  foreach ($matches as $m)
    $terms[$m[1]]=$m[2];
  $k=$nb==1?1:(preg_match('/(?<!1)[234]$/',$nb)?2:5);
  return isset($terms[$k]) ? str_replace('#',$nb,$terms[$k]) : $nb;
});

// usage
$line='{0, polish, 1 {# linia}, 2 {# linie}, 5 {# linii}}';
echo $f3->format($line,1); // 1 linia
echo $f3->format($line,2); // 2 linie
echo $f3->format($line,5); // 2 linii
echo $f3->format($line,12); // 12 linii
echo $f3->format($line,21); // 21 linii
echo $f3->format($line,22); // 22 linie
```


## Utilities

### timeago


```php
$f3->set('FORMATS.timeago',function($nb,$mod){
		if (is_string($nb))
			$nb=strtotime($nb);
		$nb=time()-$nb;
		preg_match_all('/(\w+)\s*\{\s*(.+?)\s*\}/',$mod,$matches,PREG_SET_ORDER);
		$terms=[];
		$gaps=[
			's'=>0,
			'm'=>60,
			'mm'=>60*2,
			'h'=>60*60,
			'hh'=>60*60*2,
			'd'=>60*60*24,
			'dd'=>60*60*24*2,
			'M'=>60*60*24*30,
			'MM'=>60*60*24*30*2,
			'Y'=>60*60*24*30*12,
			'YY'=>60*60*24*30*12*2,
		];
		foreach ($matches as $m)
			$terms[$m[1]]=$m[2];
		$k='s';
		foreach ($gaps as $gk=>$v)
			if ($nb>$v)
				$k=$gk;
		if (strlen($k)>1)
			$nb=round($nb/$gaps[$k[0]]);
		return isset($terms[$k]) ? str_replace('#',$nb,$terms[$k]) : $nb;
	});

	$posted='Comment was posted {0, timeago, 
		s {just now}, 
		m {a minute ago}, 
		mm {# minutes ago}, 
		h {an hour ago}, 
		hh {# hours ago}, 
		d {yesterday}
		dd {# days ago}
		M {last month}
		MM {# months ago}
		Y {last year}
		YY {# years ago}
	}';
	echo $f3->format($posted,strtotime('-30 seconds'))."\n"; // just now
	echo $f3->format($posted,strtotime('-65 seconds'))."\n"; // a minute ago
	echo $f3->format($posted,strtotime('-300 seconds'))."\n"; // 5 minutes ago
	echo $f3->format($posted,'25.10.2016')."\n"; // 8 days ago
	echo $f3->format($posted,'10.06.2016')."\n"; // 5 months ago
	echo $f3->format($posted,'02.08.2015')."\n"; // last year

```
