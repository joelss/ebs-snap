# ebs-snap


VOLID=""
ec2-consistent-snapshot \
    --region us-east-1 \
    --freeze-filesystem /data \
    --description "important data" \
    $VOLID > >(
        ec2-replicate-snapshots \
            --source us-east-1 \
            --destination us-west-2
    ) 2> >( logger -t snapshot-backups )
    
    
    
ec2-expire-snapshots \
    --keep-first-hourly 48 --keep-first-daily 14 --keep-first-weekly 8 --keep-first-monthly 24 \
    --region us-east-1 \
    $VOLID
ec2-expire-snapshots \
    --keep-first-hourly 48 --keep-first-daily 14 --keep-first-weekly 8 --keep-first-monthly 24 \
    --region us-west-2 \
    --volume-id-in-tag MasterVolumeId \
    $VOLID
