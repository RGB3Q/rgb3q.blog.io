采用数据订阅的方式相比于普通的get方法，能获得更大的性能提升

```python
while traci.simulation.getMinExpectedNumber() > 0: 
    for veh_id in traci.simulation.getDepartedIDList():
        traci.vehicle.subscribe(veh_id, [traci.constants.VAR_POSITION])
    positions = traci.vehicle.getAllSubscriptionResults()
    traci.simulationStep()
```

