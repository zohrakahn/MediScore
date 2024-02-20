# MediScore
import java.util.ArrayList;
import java.util.List;

enum AirOxygen {
  AIR,
  OXYGEN
}

enum Consciousness {
  ALERT,
  CVPU  
}

class Observations {

  AirOxygen airOxygen;
  Consciousness consciousness;
  int respirationRate;
  int spo2;
  double temperature;

  public Observations(AirOxygen airOxygen, Consciousness consciousness, int respirationRate, int spo2, double temperature) {
    this.airOxygen = airOxygen;
    this.consciousness = consciousness;
    this.respirationRate = respirationRate;
    this.spo2 = spo2;
    this.temperature = temperature;
  }
}

public class MediScoreCalculator {

  public static int calculateMediScore(Observations observations, Integer previousScore) {
    
    List<Integer> scores = new ArrayList<>();

    // Air oxygen
    if(observations.airOxygen == AirOxygen.OXYGEN) {
      scores.add(2);
    }

    // Consciousness
    if(observations.consciousness == Consciousness.CVPU) {
      scores.add(1);
    }

    // Respiration rate
    if(observations.respirationRate <= 8) {
      scores.add(3);
    } else if(observations.respirationRate <= 11) {
      scores.add(1);
    } else if(observations.respirationRate <= 20) {
      scores.add(0);
    } else if(observations.respirationRate <= 24) {
      scores.add(2);
    } else {
      scores.add(3);
    }

    // SpO2
    if(observations.spo2 <= 83) {
      scores.add(3);
    } else if(observations.spo2 <= 85) {
      scores.add(2);
    } else if(observations.spo2 <= 87) {
      scores.add(1);
    } else if(observations.spo2 <= 92 || (observations.spo2 >= 93 && observations.airOxygen == AirOxygen.AIR)) {
      scores.add(0);
    } else if(observations.spo2 <= 94) {
      scores.add(1);
    } else if(observations.spo2 <= 96) {
      scores.add(2);
    } else {
      scores.add(3);
    }

    // Temperature
    if(observations.temperature <= 35.0) {
      scores.add(3); 
    } else if(observations.temperature <= 36.0) {
      scores.add(1);
    } else if(observations.temperature <= 38.0) {
      scores.add(0);
    } else if(observations.temperature <= 39.0) {
      scores.add(1);
    } else {
      scores.add(2);
    }

    int mediScore = scores.stream().mapToInt(i -> i).sum();

    // Check for increasing score
    if(previousScore != null && mediScore - previousScore > 2) {
      System.out.println("Alert: Medi score increased by more than 2 points");
    }

    return mediScore;
  }

  public static int calculateCBGScore(double cbg, boolean isFasting) {
        
    double[] fastingRanges = {3.4, 3.5, 4.0, 5.5, 6.0};
    double[] nonFastingRanges = {4.5, 4.6, 5.9, 7.9, 9.0};
    double[] ranges = isFasting ? fastingRanges : nonFastingRanges;

    if(cbg <= ranges[0]) {
      return 3;
    } else if(cbg <= ranges[1]) {
      return 2;
    } else if(cbg <= ranges[2]) {
      return 0;
    } else if(cbg <= ranges[3]) {
      return 2;
    } else {
      return 3;
    }
  }

  public static void main(String[] args) {
        
    Observations observations = new Observations(AirOxygen.AIR, Consciousness.ALERT, 18, 95, 37.2);
    int mediScore = calculateMediScore(observations, null);
    System.out.println("Medi Score: " + mediScore);

    double cbg = 5.2;
    int cbgScore = calculateCBGScore(cbg, true);
    System.out.println("CBG Score when fasting: " + cbgScore);

  }

}
