#! /usr/bin/perl -CSDAL

use strict;
use warnings;
use JSON;
use LWP::Simple;
use POSIX qw(strftime);
use Data::Dumper;
use utf8;

my $placesURI = "http://api-test.trafikanten.no/Place/FindMatchesOfType/%s?placeType=Stop";
my $getTravelAfterByStopsURI = "http://api-test.trafikanten.no/Travel/GetTravelsAfterByStops?fromstops=%s&tostops=%s";

my $from = $ARGV[0];
my $to = $ARGV[1];

my @fromplaces = map {$_->{ID}} @{decode_json(get(sprintf($placesURI, $from)))};
my @toplaces = map {$_->{ID}} @{decode_json(get(sprintf($placesURI, $to)))};

my $travels = decode_json(get(sprintf($getTravelAfterByStopsURI, 
				      join(",", @fromplaces),
				      join(",", @toplaces))));

sub format_date {
	my ($format, $date) = (@_);
	$date =~ /Date\((\d+)/;
	return strftime($format, localtime($1/1000));
}

foreach my $option (@{$travels}) {
	print format_date("%Y-%m-%d %H%M - ", $option->{DepartureTime});
	print format_date("%H%M\n", $option->{ArrivalTime});
	foreach my $stage (@{$option->{TravelStages}}) {
		if ($stage->{Destination} ne 'Gangledd') {
			printf("  [%s-%s] Linje %s fra %s mot %s til %s\n",
			       format_date("%H%M", $stage->{DepartureTime}),
			       format_date("%H%M", $stage->{ArrivalTime}),
			       $stage->{LineName},
			       $stage->{DepartureStop}->{Name},
			       $stage->{Destination},
			       $stage->{ArrivalStop}->{Name});
		} else {
			printf("  [%s-%s] Gå fra %s til %s\n",
			       format_date("%H%M", $stage->{DepartureTime}),
			       format_date("%H%M", $stage->{ArrivalTime}),
			       $stage->{DepartureStop}->{Name},
			       $stage->{ArrivalStop}->{Name});

		}
	}
}


