import boto3

def copy_rds_cluster_snapshot_to_another_region(event, context):
    # Extract relevant information from the EventBridge event
    snapshot_id = event['detail']['SourceArn'].split(':snapshot:')[1]
    source_region = event['region']
    destination_region = 'your_destination_region'  # Specify your destination region
    
    # Create a boto3 client for the RDS service in the source region
    source_rds = boto3.client('rds', region_name=source_region)
    
    # Describe the cluster snapshot to get its details
    snapshot_info = source_rds.describe_db_cluster_snapshots(
        DBClusterSnapshotIdentifier=snapshot_id)['DBClusterSnapshots'][0]
    
    # Create a boto3 client for the RDS service in the destination region
    destination_rds = boto3.client('rds', region_name=destination_region)
    
    # Copy the cluster snapshot to the destination region
    response = destination_rds.copy_db_cluster_snapshot(
        SourceDBClusterSnapshotIdentifier=snapshot_id,
        SourceRegion=source_region,
        TargetDBClusterSnapshotIdentifier=snapshot_info['DBClusterSnapshotIdentifier']
    )
    
    # Log the result
    print("Cluster snapshot copied to another region. New Snapshot ID:", response['DBClusterSnapshot']['DBClusterSnapshotIdentifier'])

    # Return a response
    return {
        'statusCode': 200,
        'body': 'Cluster snapshot copied to another region successfully.'
    }

