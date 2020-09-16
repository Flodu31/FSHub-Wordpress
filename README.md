# FSHub-Wordpress
Integrate FSHub into your Wordpress website

1. Install Insert PHP Code Snippet extension => https://xyzscripts.com/
2. Create a new PHP Code snippet
3. Add the following code:

```
<?php
$curl = curl_init();
curl_setopt_array($curl, array(
    CURLOPT_URL => "https://fshub.io/api/v3/airline/theIdOfYourAirline/flight",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_TIMEOUT => 30,
    CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
    CURLOPT_CUSTOMREQUEST => "GET",
    CURLOPT_HTTPHEADER => array(
        'Content-Type: application/json',
        'X-Pilot-Token: yourtoken'
    ),
  ));

$response = curl_exec($curl);
$result = json_decode($response, true);
$err = curl_error($curl);

curl_close($curl);

$totalId = count ($result['data']);

?>
<!DOCTYPE html>
<html lang="en">
<head>
  <title>FSHub</title>
  <link href="/app.css" rel="stylesheet">
</head>
<body>
<div class="col-md-12">
<h3>Recent flights: 
    <a href="https://fshub.io/airline/SPP/flights">View all</a>
</h3>
<div class="panel panel-profile">
    <table class="table table-hover">
        <thead>
            <tr>
                <th>Date</th>
                <th>Departure</th>
                <th></th>
                <th>Arrival</th>
                <th>Pilot</th>
                <th>Aircraft</th>
                <th>Landing rate</th>
                <th>Distance</th>
                <th>Flight time</th>
                <th></th>
            </tr>
        </thead>
        <tbody>
          <?php
            for ($i = 1; $i <= $totalId; $i++) {
               $date=$result['data'][$i]['arrival']['time'];
               $search  = 'T';
               $replace = '/';
               $newdate=str_replace($search, $replace, $date);
               $exd = DateTime::createFromFormat('Y-m-d/G:i:s.uZ', $newdate);
               $exd = date_format($exd, 'dMy Hi');
                echo '<tr>';
                  //echo '<td>' . $result['data'][$i]['arrival']['time'] . 'Z</td>';
                  echo '<td>' . $exd . 'Z</td>';
                  echo '<td><a href="https://fshub.io/airport/' . $result['data'][$i]['departure']['icao'] . '/overview">' . $result['data'][$i]['departure']['icao'] . '</a></td>';
                  echo '<td><i class="fa fa-long-arrow-right"></i></td>';
                  echo '<td><a href="https://fshub.io/airport/' . $result['data'][$i]['arrival']['icao'] . '/overview">' . $result['data'][$i]['arrival']['icao'] . '</a></td>';
                  echo '<td>' . $result['data'][$i]['user']['name'] . '</td>';
                  echo '<td>' . $result['data'][$i]['aircraft']['name'] . '</td>';
                  echo '<td>' . $result['data'][$i]['landing_rate'] . ' <abbr title="Feet per minute">ft/m</abbr></td>';
                  echo '<td>' . $result['data'][$i]['distance']['nm'] . ' <abbr title="Nautical miles">nm</abbr></td>';
                  echo '<td>' . gmdate("H:i", $result['data'][$i]['time']) . '</td>';
                  echo '<td><a href="https://fshub.io/flight/' . $result['data'][$i]['id'] . '/report" title="View flight report">View</a></td>';
                echo '</tr>';
            }
          ?>
          </tbody>
      </table>
</body>
</html>
```

4. Update **theIdOfYourAirline** with the ID of your airline and **yourtoken** with your token. You can also update the URL **https://fshub.io/airline/SPP/flights** with the full URL of your VA.
